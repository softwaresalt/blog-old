# Function App API Pattern: DB to POCO Data Population





CREATE TYPE [fmo].[OperationLog] AS  TABLE (
    [OperationQueueID]            INT           NOT NULL,
    [SourceFileName]              [sysname]     NULL,
    [SourceFileSizeBytes]         INT           NULL,
    [SourceFileLastModifiedDate]  SMALLDATETIME NULL,
    [DestinationFileName]         [sysname]     NULL,
    [DestinationFileSizeBytes]    INT           NULL,
    [DateLogged]                  DATETIME      NOT NULL
);
CREATE TYPE [fmo].[OperationErrorLog] AS  TABLE (
    [OperationQueueID]    INT           NOT NULL,
    [SourceFileName]      [sysname]     NULL,
    [DestinationFileName] [sysname]     NULL,
    [Source]              VARCHAR (256) NULL,
    [Message]             VARCHAR (512) NOT NULL,
    [StackTrace]          VARCHAR (MAX) NULL,
    [DateLogged]          DATETIME      NOT NULL
);

using System;
using System.Collections;
using System.Collections.Generic;
using System.IO;
using System.Data;
using System.Data.SqlClient;
using System.Configuration;
using HMA.EventLogging;

namespace HMA.FileManagementOperations
{
	/// <summary>
	/// File Management Operations; entry point for all FMO functions
	/// </summary>
	public static class FMO
	{
		#region DataStructures
		private static string applicationCode;
		private static string connectionString;
		internal enum TrinaryType
		{
			Null,
			True,
			False
		}

		/// <summary>
		/// Enum of location types currently handled by FMO
		/// </summary>
		internal enum LocationType
		{
			Invalid,
			UNC,
			FTP,
			SFTP,
			SSDB,
			FILE
		}

		/// <summary>
		/// Enum of operation types currrently handled by FMO
		/// </summary>
		internal enum OperationType
		{
			Invalid,
			Decompress,
			Compress,
			Decrypt,
			Encrypt,
			Copy,
			Move,
			Archive,
			Remove,
			ETL,
			Rename
		}

		/// <summary>
		/// Enum of parameter types currently handled by FMO
		/// </summary>
		internal enum ParameterType
		{
			Invalid,
			ZipEncryptionType,
			ZipEncryptionAlgorithm,
			StrongEncryptionAlgorithm
		}

		/// <summary>
		/// Enum contains the names of the fields returned from the SQL stored procedure GetOperationQueue.
		/// Order of enum values is specific to order of result set returned by stored procedure.
		/// </summary>
		internal enum GetOperationQueueProcField
		{
			OperationQueueID,
			OperationTypeCode,
			ProcessGroup,
			Sequence,
			OrganizationCode,
			FormatCode,
			VendorCode,
			FromURL,
			SubPath,
			Port,
			FromLocType,
			FromFileMask,
			ToURL,
			ToLocType,
			ToFileExtension,
			FileStartEffectiveDate,
			FileEndEffectiveDate
		}

		/// <summary>
		/// Enum contains the names of the fields returned from the SQL stored procedure GetOperationParameter.
		/// Order of enum values is specific to order of result set returned by stored procedure.
		/// </summary>
		internal enum GetOperationParameterProcField
		{
			OperationQueueID,
			ParameterName,
			ParameterValue,
			ParameterTypeCode,
			ParameterTypeSql,
			ParameterTypeNet
		}

		/// <summary>
		/// Parameter class (data structure): contains parameter properties returned in conjunction with a work queue entry
		/// </summary>
		internal class Parameter
		{
			internal Int32 OperationQueueID;
			internal string ParameterName;
			internal string ParameterValue;
			internal ParameterType ParameterType;
			internal string ParameterTypeSql;
			internal string ParameterTypeNet;
			/// <summary>
			/// Default constructor for Parameter class
			/// </summary>
			/// <param name="sdr">SQL DataReader object pointer where passed for a specific row</param>
			internal Parameter(SqlDataReader sdr)
			{
				OperationQueueID = sdr.GetInt32((int)GetOperationParameterProcField.OperationQueueID);
				ParameterName = sdr.GetString((int)GetOperationParameterProcField.ParameterName);
				ParameterValue = sdr.GetString((int)GetOperationParameterProcField.ParameterValue);
				ParameterType = Translation.GetParameterTypeCodeEnum(sdr.GetString((int)GetOperationParameterProcField.ParameterTypeCode));
				ParameterTypeSql = sdr.GetString((int)GetOperationParameterProcField.ParameterTypeSql);
				ParameterTypeNet = sdr.GetString((int)GetOperationParameterProcField.ParameterTypeNet);
			}

		}

		/// <summary>
		/// WorkQueueEntry class (data structure): contains properties of a work queue entry
		/// </summary>
		internal class Operation
		{
			internal int OperationQueueID;
			internal OperationType OperationType;
			internal int ProcessGroup;
			internal int Sequence;
			internal string OrganizationCode;
			internal string FormatCode;
			internal string VendorCode;
			internal string FromPath;
			internal string SubPath;
			internal int Port;
			internal LocationType FromPathType;
			internal string FromFileMask;
			internal string ToPath;
			internal LocationType ToPathType;
			internal string ToFileExtension;
			internal DateTime FileStartEffectiveDate;
			internal DateTime FileEndEffectiveDate;
			internal object ProcessorTypeIn;
			internal object ProcessorTypeOut;
			internal List<Parameter> Parameters;

			/// <summary>
			/// WorkQueueEntry default constructor
			/// </summary>
			/// <param name="sdr">SQL DataReader object pointer where passed for a specific row</param>
			/// <param name="parameterList">List of parameters returned for all work queue entries</param>
			internal Operation(SqlDataReader sdr, List<Parameter> parameterList)
			{
				OperationQueueID = sdr.GetInt32((int)GetOperationQueueProcField.OperationQueueID);
				OperationType = Translation.GetOperationTypeCodeEnum(sdr.GetString((int)GetOperationQueueProcField.OperationTypeCode));
				ProcessGroup = sdr.GetByte((int)GetOperationQueueProcField.ProcessGroup);
				Sequence = sdr.GetByte((int)GetOperationQueueProcField.Sequence);
				OrganizationCode = sdr.GetString((int)GetOperationQueueProcField.OrganizationCode);
				FormatCode = sdr.GetString((int)GetOperationQueueProcField.FormatCode);
				VendorCode = sdr.GetString((int)GetOperationQueueProcField.VendorCode);
				FromPath = sdr.GetString((int)GetOperationQueueProcField.FromURL);
				SubPath = sdr.GetString((int)GetOperationQueueProcField.SubPath);
				Port = sdr.GetInt32((int)GetOperationQueueProcField.Port);
				FromPathType = Translation.GetPathTypeCodeEnum(sdr.GetString((int)GetOperationQueueProcField.FromLocType));
				FromFileMask = sdr.GetString((int)GetOperationQueueProcField.FromFileMask);
				ToPath = sdr.GetString((int)GetOperationQueueProcField.ToURL);
				ToPathType = Translation.GetPathTypeCodeEnum(sdr.GetString((int)GetOperationQueueProcField.ToLocType));
				ToFileExtension = sdr.GetString((int)GetOperationQueueProcField.ToFileExtension);
				FileStartEffectiveDate = sdr.GetDateTime((int)GetOperationQueueProcField.FileStartEffectiveDate);
				FileEndEffectiveDate = sdr.GetDateTime((int)GetOperationQueueProcField.FileEndEffectiveDate);
				// Get filtered list of parameters for this specific work queue entry; returns empty list of type Parameter if none exist
				Parameters = parameterList.FindAll(
					delegate(Parameter param)
					{
						return (param.OperationQueueID == OperationQueueID);
					}
				);

				switch (FromPathType)
				{
					case LocationType.FTP:
						ProcessorTypeIn = new ProcessorTypeFTP();
						break;
					case LocationType.SFTP:
						ProcessorTypeIn = new ProcessorTypeSFTP();
						break;
					case LocationType.SSDB:
						ProcessorTypeIn = new ProcessorTypeQLDB();
						break;
					case LocationType.FILE:
						ProcessorTypeIn = new ProcessorTypeFileExec();
						break;
					default:
						ProcessorTypeIn = new ProcessorTypeUNC();
						break;
				}

				switch (ToPathType)
				{
					case LocationType.FTP:
						ProcessorTypeOut = new ProcessorTypeFTP();
						break;
					case LocationType.SFTP:
						ProcessorTypeOut = new ProcessorTypeSFTP();
						break;
					case LocationType.SSDB:
						ProcessorTypeOut = new ProcessorTypeQLDB();
						break;
					case LocationType.FILE:
						ProcessorTypeOut = new ProcessorTypeFileExec();
						break;
					default:
						ProcessorTypeOut = new ProcessorTypeUNC();
						break;
				}
			}
		}
		#endregion

		/// <summary>
		/// FMO entry point method for service and console app
		/// </summary>
		/// <param name="ServiceName">Service passes the its name to the method for event logging</param>
		/// <returns>true/false (bool)</returns>
		public static bool Run(string ServiceName)
		{
			SBUtils.Unit.SetLicenseKey
			("6431800557CFA6D2BF169D9719253A2AFF76FDBE30D64A313822B502978979EFA4D3846C01BB899577DC14751910DE8C35C0F19904A94F787BA79C2E26F7DA10C39D885303D75FAF070A2FA32F537B0DC7DDAB1E3C78147F2DE7E1D5773CFBB9F17A03161FA487B28658378CFB1E7F2BBA629F4C05C83F1E2919BAA00A9E375AA70D2E6F698E4EC32CC88203ECFFF2AAB91AB95F8F68D798D8446C5EDB853C19F90BA7D74E908FCED54B5FBC2B28DA2A7B0934DF294BB3AF44FB52FA1DD388E5FEF30438D51E3E25F6C13A402073A501D3C991BED667BA7FA7FB9EA5DC6CE2090A155B25D78A1282FE53026BFCF716C1A6913C5A7C97485350D8B6E5F1D84B73");

			//Declare work queue entry and work queue collection objects
			Queue<Operation> workQueue;
			Operation operation;

			//Initialize datatable object for OperationLog & OperationErrorLog info
			DataTable operationLog = initializeOperationLog();
			DataTable operationErrorLog = initializeOperationErrorLog();

			//Declare logging operation booleans
			bool isOperationLogged = false, isOperationErrorLogged = false;

			try
			{
				//First, check for any log file dumps
				//If found, reattempt to save to DB prior to building a workqueue
				sniffForCheckpointFilesAndSave(ServiceName);

				//Retrieve work queue collection
				workQueue = getWorkQueue();

				//iterate retrieved work queue
				while (workQueue.Count > 0)
				{
					//Dequeue and dispatch WorkQueueEntry
					operation = workQueue.Dequeue();
					switch (operation.OperationType)
					{
						case OperationType.Decompress:
							ZIP.Decompress(operation, operationLog, operationErrorLog);
							break;
						case OperationType.Compress:
							ZIP.Compress(operation, operationLog, operationErrorLog);
							break;
						case OperationType.Decrypt:
							PGP.Decrypt(operation, operationLog, operationErrorLog);
							break;
						case OperationType.Encrypt:
							PGP.Encrypt(operation, operationLog, operationErrorLog);
							break;
						case OperationType.Copy:
						case OperationType.Move:
						case OperationType.Remove:
						case OperationType.Archive:
							moveProcessedFiles(operation, operation.ProcessorTypeIn, operation.ProcessorTypeOut, operationLog, operationErrorLog);
							break;
						default:
							//Build error log entry
							operationErrorLog.Rows.Add(
								new object[] { 
									operation.OperationQueueID, 
									null, 
									null, 
									"AppCode: " + FMO.ApplicationCode + "; OrgCode: " + operation.OrganizationCode, 
									"The operation type is invalid: " + operation.OperationType.ToString(), 
									null, 
									DateTime.Now 
								}
							);
							break;
					}
				}
				try
				{
					//Log successful operations; saves checkpoint on logging failure
					isOperationLogged = logOperation(operationLog, ServiceName);
					//Log operation failures; saves checkpoint on logging failure
					isOperationErrorLogged = logOperationError(operationErrorLog, ServiceName);
				}
				catch
				{
					//Catch the scenario that logOperationError did not get a chance to save or checkpoint
					if (isOperationLogged)
					{
						logOperationError(operationErrorLog, ServiceName);
					}
					throw;
				}
			}
			catch (Exception e)
			{
				//Don't forward the exceptions; log them and exit gracefully.
				EventLogger.LogError("FMO.Run: ", e);
			}
			finally
			{
				operationLog.Dispose();
				operationErrorLog.Dispose();
			}
			return true;
		}

		/// <summary>
		/// Dispatch method: dynamically determines type of in/out processors and dispatches to strongly typed method by signature
		/// </summary>
				/// <param name="operation">Work queue entry data structure</param>
		/// <param name="ProcessorTypeIn">Generic processor type [object]</param>
		/// <param name="ProcessorTypeOut">Generic processor type [object]</param>
		/// <param name="operationLog">Operation log [DataTable]</param>
		/// <param name="operationErrorLog">Operation error log [DataTable]</param>
		private static void moveProcessedFiles(Operation operation, dynamic ProcessorTypeIn, dynamic ProcessorTypeOut, DataTable operationLog, DataTable operationErrorLog)
		{
			moveProcessedFiles(operation, ProcessorTypeIn, ProcessorTypeOut, operationLog, operationErrorLog);
		}

		private static void moveProcessedFiles(Operation operation, ProcessorTypeUNC ProcessorTypeIn, ProcessorTypeUNC ProcessorTypeOut, DataTable operationLog, DataTable operationErrorLog)
		{
			FileIO.MoveFiles(operation, operationLog, operationErrorLog);
		}

		private static void moveProcessedFiles(Operation operation, ProcessorTypeFTP ProcessorTypeIn, ProcessorTypeUNC ProcessorTypeOut, DataTable operationLog, DataTable operationErrorLog)
		{
			FTP.FetchFTP(operation, operationLog, operationErrorLog);
		}

		private static void moveProcessedFiles(Operation operation, ProcessorTypeUNC ProcessorTypeIn, ProcessorTypeFTP ProcessorTypeOut, DataTable operationLog, DataTable operationErrorLog)
		{
			FTP.SendFTP(operation, operationLog, operationErrorLog);
		}

		private static void moveProcessedFiles(Operation operation, ProcessorTypeSFTP ProcessorTypeIn, ProcessorTypeUNC ProcessorTypeOut, DataTable operationLog, DataTable operationErrorLog)
		{
			FTP.FetchSFTP(operation, operationLog, operationErrorLog);
		}

		private static void moveProcessedFiles(Operation operation, ProcessorTypeUNC ProcessorTypeIn, ProcessorTypeSFTP ProcessorTypeOut, DataTable operationLog, DataTable operationErrorLog)
		{
			FTP.SendSFTP(operation, operationLog, operationErrorLog);
		}

		private static DataTable initializeOperationLog()
		{
			using (DataTable operationLog = new DataTable("OperationLog"))
			{
				operationLog.Columns.Add("OperationQueueID", typeof(System.Int32)).AllowDBNull = false;
				operationLog.Columns.Add("SourceFileName", typeof(System.String)).AllowDBNull = true;
				operationLog.Columns.Add("SourceFileSizeBytes", typeof(System.Int32)).AllowDBNull = true;
				operationLog.Columns.Add("SourceFileLastModifiedDate", typeof(System.DateTime)).AllowDBNull = true;
				operationLog.Columns.Add("DestinationFileName", typeof(System.String)).AllowDBNull = true;
				operationLog.Columns.Add("DestinationFileSizeBytes", typeof(System.Int32)).AllowDBNull = true;
				operationLog.Columns.Add("DateLogged", typeof(System.DateTime)).AllowDBNull = false;
				return operationLog;
			}
		}

		private static DataTable initializeOperationErrorLog()
		{
			using (DataTable operationLog = new DataTable("OperationErrorLog"))
			{
				operationLog.Columns.Add("OperationQueueID", typeof(System.Int32)).AllowDBNull = false;
				operationLog.Columns.Add("SourceFileName", typeof(System.String)).AllowDBNull = true;
				operationLog.Columns.Add("DestinationFileName", typeof(System.String)).AllowDBNull = true;
				operationLog.Columns.Add("Source", typeof(System.String)).AllowDBNull = true;
				operationLog.Columns.Add("Message", typeof(System.String)).AllowDBNull = false;
				operationLog.Columns.Add("StackTrace", typeof(System.String)).AllowDBNull = true;
				operationLog.Columns.Add("DateLogged", typeof(System.DateTime)).AllowDBNull = false;
				return operationLog;
			}
		}

		/// <summary>
		/// Retrieves queue of work (operations) from FMO database; builds work queue entries and adds to queue
		/// </summary>
		/// <returns>Queue of work queue entries</returns>
		private static Queue<Operation> getWorkQueue()
		{
			Queue<Operation> workQueue = new Queue<Operation>();
			List<Parameter> parameterList = new List<Parameter>();
			//Get database connection string and stored procedure name from configuration file.
			string dbConn = ConnectionString;
			string procName = ConfigurationManager.AppSettings.Get("GetWorkQueue");

			//Initialize connection & command in using statement
			using (SqlConnection connection = new SqlConnection(dbConn))
			{
				using (SqlCommand cmd = new SqlCommand())
				{
					cmd.CommandType = CommandType.StoredProcedure;
					cmd.CommandText = procName;
					cmd.Connection = connection;
					cmd.Parameters.Add("@SetQueueStatus", SqlDbType.Bit).Value = 1; //True
					connection.Open();

					SqlDataReader dr = cmd.ExecuteReader();
					while (dr.Read())
					{
						parameterList.Add(new Parameter(dr));
					}
					dr.NextResult(); //Get the next result set (the actual operations)
					while (dr.Read())
					{
						workQueue.Enqueue(new Operation(dr, parameterList));
					}
				}
			}

			return workQueue;
		}

		/// <summary>
		/// Log successful operations back to database
		/// </summary>
		/// <param name="operationLog">Success log [DataTable]</param>
		/// <param name="ServiceName">Name of service provided in case necessary to log to Event Log</param>
		private static bool logOperation(DataTable operationLog, string ServiceName)
		{
			try
			{
				if (operationLog.Rows.Count > 0)
				{
					//Get database connection string and stored procedure name from configuration file.
					string dbConn = ConnectionString;
					string procName = ConfigurationManager.AppSettings.Get("SaveOperationLog");

					//Initialize connection & command in using statement
					using (SqlConnection connection = new SqlConnection(dbConn))
					{
						using (SqlCommand cmd = new SqlCommand())
						{
							cmd.CommandType = CommandType.StoredProcedure;
							cmd.CommandText = procName;
							cmd.Connection = connection;
							cmd.Parameters.Add("@OperationLogEntry", SqlDbType.Structured).Value = operationLog;

							SqlParameter returnValue = cmd.Parameters.Add("@RETURN_VALUE", SqlDbType.Int);
							returnValue.Direction = ParameterDirection.ReturnValue;
							connection.Open();
							cmd.ExecuteNonQuery();

						}
					}
				}
			}
			catch
			{
				// In case of failure to connect to database, save the log results to disc as a checkpoint file.
				saveCheckpoint(operationLog, OperationErrorLogCheckpointFile, ServiceName);
				throw;
			}
			return true;
		}

		/// <summary>
		/// Log failed operations (errors) back to database
		/// </summary>
		/// <param name="operationErrorLog">Error log [DataTable]</param>
		/// <param name="ServiceName">Name of service provided in case necessary to log to Event Log</param>
		private static bool logOperationError(DataTable operationErrorLog, string ServiceName)
		{
			try
			{
				if (operationErrorLog.Rows.Count > 0)
				{
					//Get database connection string and stored procedure name from configuration file.
					string dbConn = ConnectionString;
					string procName = ConfigurationManager.AppSettings.Get("SaveOperationErrorLog");

					//Initialize connection & command in using statement
					using (SqlConnection connection = new SqlConnection(dbConn))
					{
						using (SqlCommand cmd = new SqlCommand())
						{
							cmd.CommandType = CommandType.StoredProcedure;
							cmd.CommandText = procName;
							cmd.Connection = connection;
							cmd.Parameters.Add("@OperationErrorLogEntry", SqlDbType.Structured).Value = operationErrorLog;

							SqlParameter returnValue = cmd.Parameters.Add("@RETURN_VALUE", SqlDbType.Int);
							returnValue.Direction = ParameterDirection.ReturnValue;
							connection.Open();
							cmd.ExecuteNonQuery();

						}
					}
				}
			}
			catch
			{
				// In case of failure to connect to database, save the log results to disc as a checkpoint file.
				saveCheckpoint(operationErrorLog, OperationErrorLogCheckpointFile, ServiceName);
				throw;
			}
			return true;
		}

		/// <summary>
		/// Saves log DataTable objects as checkpoint file (XML)
		/// </summary>
		/// <param name="log">Log object as DataTable</param>
		/// <param name="filenamePattern">File name template provided from config file</param>
		/// <param name="serviceName">Name of service provided in case necessary to log to Event Log</param>
		private static void saveCheckpoint(DataTable log, string filenamePattern, string serviceName)
		{
			try
			{
				if (!Directory.Exists(CheckpointLocation))
				{
					Directory.CreateDirectory(CheckpointLocation);
				}
				string uniqueID = DateTime.Now.ToString("s").Replace(":", ""); //Sortable date/time pattern minus invalid filename character
				string filepath = CheckpointLocation + "\\" + string.Format(filenamePattern, uniqueID);
				log.WriteXml(filepath);
			}
			catch (Exception e)
			{
				//Add code here to raise exception to Application Event log
				EventLogger.LogError("FMO.saveCheckpoint: ", e);
			}
		}

		/// <summary>
		/// Checks the reserved CheckPoint directory for any operation and error log files
		/// that may have been saved from previous operations that were unsuccessfully saved back to the database.
		/// If it finds any, attempts to save logs to the database; resaves logs on failure to connect to database.
		/// </summary>
		/// <param name="ServiceName">Name of the service if necessary to log to event log</param>
		private static void sniffForCheckpointFilesAndSave(string ServiceName)
		{
			//Initialize logging data tables
			DataTable operationLog = initializeOperationLog();
			DataTable operationErrorLog = initializeOperationErrorLog();
			//Get file masks from file format saved in configuration file
			string operationLogFileMask = string.Format(OperationLogCheckpointFile, "*");
			string operationErrorLogFileMask = string.Format(OperationErrorLogCheckpointFile, "*");
			//Initialize directory object for checkpoint location
			DirectoryInfo directory = new DirectoryInfo(CheckpointLocation);

			//Get enumeration of operation log files dumped to disk (if any)
			IEnumerable<FileInfo> files = directory.EnumerateFiles(operationLogFileMask);
			foreach (FileInfo file in files)
			{
				//Reload the dump of the operation log and reattempt to save to DB; remove before DB call
				//If file delete fails; whole operation should fail
				operationLog.ReadXml(file.FullName);
				file.Delete();
				logOperation(operationLog, ServiceName);
			}

			//Get enumeration of operation error log files dumped to disk (if any)
			files = directory.EnumerateFiles(operationErrorLogFileMask);
			foreach (FileInfo file in files)
			{
				//Reload the dump of the operation error log and reattempt to save to DB; remove before DB call
				//If file delete fails; whole operation should fail
				operationErrorLog.ReadXml(file.FullName);
				file.Delete();
				logOperationError(operationErrorLog, ServiceName);
			}
		}

		internal static DataTable InitializeOrganizationPath()
		{
			using (DataTable orgKey = new DataTable("OrganizationPathWorkflow"))
			{
				orgKey.Columns.Add("OrgCode", typeof(System.String));
				orgKey.Columns.Add("SourceFileName", typeof(System.String));
				orgKey.Columns.Add("FromURL", typeof(System.String));
				orgKey.Columns.Add("ToURL", typeof(System.String));
				return orgKey;
			}
		}

		/// <summary>
		/// Checks to see if file has already been processed by first that file name provided is in provided list of unprocessed files
		/// Also checks that file does not have the Hidden attribute set, which indicates file is not to be processed.
		/// </summary>
		/// <param name="unprocessedFiles">String list of unprocessed files against which to check file on disc</param>
		/// <param name="file">FileInfo object for file on disc</param>
		/// <returns>true/false [bool]</returns>
		internal static bool IsProcessed(List<string> unprocessedFiles, FileInfo file)
		{
			//Check if the file hasn't already been processed; process if not
			//Also, we don't process files with the Hidden attribute
			bool isProcessed = (unprocessedFiles.IndexOf(file.Name) == -1);
			bool isHidden = (FileAttributes.Hidden == (file.Attributes & FileAttributes.Hidden));
			return (isProcessed || isHidden);
		}

		/// <summary>
		/// Takes structured source of files from a given location and destination location for a given org code
		/// and returns the files by name that have not already been processed through this pathway.
		/// </summary>
		/// <param name="orgPath">Structured [DataTable] source of file names, to/from location paths, and org code</param>
		/// <returns>String list of file names</returns>
		internal static List<string> UnprocessedFiles(DataTable orgPath)
		{
			//Call database to get list of files not already processed (already in log table)
			string dbConn = ConnectionString;
			string procName = ConfigurationManager.AppSettings.Get("CheckReturnUnprocessedFiles");
			List<string> fileNames = new List<string>();
			//Initialize connection & command in using statement
			using (SqlConnection connection = new SqlConnection(dbConn))
			{
				using (SqlCommand cmd = new SqlCommand())
				{
					cmd.CommandType = CommandType.StoredProcedure;
					cmd.CommandText = procName;
					cmd.Connection = connection;
					cmd.Parameters.Add("@OperationPathEntries", SqlDbType.Structured).Value = orgPath;
					connection.Open();
					SqlDataReader dr = cmd.ExecuteReader();
					while (dr.Read())
					{
						fileNames.Add(dr.GetString(0));
					}
				}
			}
			return fileNames;
		}

		internal static string CheckpointLocation
		{
			get { return ConfigurationManager.AppSettings.Get("CheckpointLocation");}
		}

		internal static string OperationLogCheckpointFile
		{
			get { return ConfigurationManager.AppSettings.Get("OperationLogCheckpointFile"); }
		}

		internal static string OperationErrorLogCheckpointFile
		{
			get { return ConfigurationManager.AppSettings.Get("OperationErrorLogCheckpointFile"); }
		}

		internal static string ConnectionString
		{
			get 
			{
				if (connectionString == null)
				{
					connectionString = ConfigurationManager.ConnectionStrings["FMO"].ConnectionString;
				}
				return connectionString; 
			}
		}

		internal static string CredentialProcedure
		{
			get { return ConfigurationManager.AppSettings.Get("GetCredentials"); }
		}

		internal static string ApplicationCode
		{
			get 
			{
				if (applicationCode == null)
				{
					applicationCode = ConfigurationManager.AppSettings.Get("ApplicationCode");
				}
				return applicationCode; 
			}
		}

		/// <summary>
		/// Contains methods to convert enums to corresponding code values originating from database domain tables and visa versa.
		/// </summary>
		internal class Translation
		{

			/// <summary>
			/// Converts database code to enum for OperationType
			/// </summary>
			/// <param name="code">4 character operation type database code</param>
			/// <returns>OperationType enum</returns>
			internal static OperationType GetOperationTypeCodeEnum(string code)
			{
				switch (code)
				{
					case "DCMP":
						return OperationType.Decompress;
					case "CMPR":
						return OperationType.Compress;
					case "DCRP":
						return OperationType.Decrypt;
					case "NCRP":
						return OperationType.Encrypt;
					case "COPY":
						return OperationType.Copy;
					case "MOVE":
						return OperationType.Move;
					case "RCHV":
						return OperationType.Archive;
					case "REMV":
						return OperationType.Remove;
					case "LOAD":
						return OperationType.ETL;
					case "RENM":
						return OperationType.Rename;
					default:
						return OperationType.Invalid;
				}
			}

			/// <summary>
			/// Converts database code to enum for LocationType
			/// </summary>
			/// <param name="code">Variable character length location type database code</param>
			/// <returns>LocationType enum</returns>
			internal static LocationType GetPathTypeCodeEnum(string code)
			{
				switch (code)
				{
					case "UNC":
						return LocationType.UNC;
					case "FTP":
						return LocationType.FTP;
					case "SFTP":
						return LocationType.SFTP;
					case "SSDB":
						return LocationType.SSDB;
					case "FILE":
						return LocationType.FILE;
					default:
						return LocationType.Invalid;
				}
			}

			/// <summary>
			/// Converts database code to enum for ParameterType
			/// </summary>
			/// <param name="code">Variable character length parameter type database code</param>
			/// <returns>ParameterType enum</returns>
			internal static ParameterType GetParameterTypeCodeEnum(string code)
			{
				switch (code)
				{
					case "ZCRPTYPE":
						return ParameterType.ZipEncryptionType;
					case "ZCRPTAGM":
						return ParameterType.ZipEncryptionAlgorithm;
					case "SCRPTAGM":
						return ParameterType.StrongEncryptionAlgorithm;
					default:
						return ParameterType.Invalid;
				}
			}

			internal static TrinaryType GetTrinaryTypeCodeEnum(bool bit)
			{
				switch (bit)
				{
					case true:
						return TrinaryType.True;
					case false:
						return TrinaryType.False;
					default:
						return TrinaryType.Null;
				}
			}
		}

		private class ProcessorTypeFTP { }
		private class ProcessorTypeSFTP { }
		private class ProcessorTypeFTPS { }
		private class ProcessorTypeUNC { }
		private class ProcessorTypeQLDB { }
		private class ProcessorTypeFileExec { }
	}
}
