# Moving Data from Parquet file to Azure SQL DWH

I recently worked with a customer on a project where we tried to move data from parquet file to Azure SQL DWH (dedicated pool). However, we were getting the below error. So if you are also facing the same issue, then keep reading. 

Error:- 

```
{
    "errorCode": "2200",
    "message": "ErrorCode=UserErrorSqlDWCopyCommandError,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=SQL DW Copy Command operation failed with error 'HdfsBridge::recordReaderFillBuffer - Unexpected error encountered filling record reader buffer: HadoopUnexpectedException: Request failed due to an internal error that occurred during map-reduce job execution. Exception message = [Parquet column with primitive type [optional binary XYZA (UTF8)] cannot be convertedto [DECIMAL] because parquet type is not annottated with decimal metadata].',Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Data.SqlClient.SqlException,Message=HdfsBridge::recordReaderFillBuffer - Unexpected error encountered filling record reader buffer: HadoopUnexpectedException: Request failed due to an internal error that occurred during map-reduce job execution. Exception message = [Parquet column with primitive type [optional binary XYZA (UTF8)] cannot be convertedto [DECIMAL] because parquet type is not annottated with decimal metadata].,Source=.Net SqlClient Data Provider,SqlErrorNumber=106000,Class=16,ErrorCode=-2146232060,State=1,Errors=[{Class=16,Number=106000,State=1,Message=HdfsBridge::recordReaderFillBuffer - Unexpected error encountered filling record reader buffer: HadoopUnexpectedException: Request failed due to an internal error that occurred during map-reduce job execution. Exception message = [Parquet column with primitive type [optional binary XYZA (UTF8)] cannot be convertedto [DECIMAL] because parquet type is not annottated with decimal metadata].,},],'",
    "failureType": "UserError",
    "target": "Copy data1",
    "details": []
}
```

In this case, the data type from the parquet file was UTF8, but the column was defined as a decimal on the SQL DWH side. Hence the error. Even if you use Polybase, you will get a similar error.


```
{
    "errorCode": "2200",
    "message": "ErrorCode=PolybaseOperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Error happened when loading data into SQL Data Warehouse. Operation: 'Polybase operation'.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Data.SqlClient.SqlException,Message=HdfsBridge::recordReaderFillBuffer - Unexpected error encountered filling record reader buffer: HadoopUnexpectedException: Request failed due to an internal error that occurred during map-reduce job execution. Exception message = [Parquet column with primitive type [optional binary XYZA (UTF8)] cannot be convertedto [DECIMAL] because parquet type is not annottated with decimal metadata].,Source=.Net SqlClient Data Provider,SqlErrorNumber=106000,Class=16,ErrorCode=-2146232060,State=1,Errors=[{Class=16,Number=106000,State=1,Message=HdfsBridge::recordReaderFillBuffer - Unexpected error encountered filling record reader buffer: HadoopUnexpectedException: Request failed due to an internal error that occurred during map-reduce job execution. Exception message = [Parquet column with primitive type [optional binary XYZA (UTF8)] cannot be convertedto [DECIMAL] because parquet type is not annottated with decimal metadata].,},],'",
    "failureType": "UserError",
    "target": "Copy data1",
    "details": []
}
```
If you noticed the above error, the error happened during loading data into SQL DWH.

As per the [Microsoft doc](https://docs.microsoft.com/en-us/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading#define-the-tables), the mapping of UTF8 is mapped to nvarchar. 

So 1) If you create a table with nvarchar(max) then data load successfully 2) Also, if create a mapping data flow to cast the data type before loading in SQL DWH; that also works.

Thanks for reading! 
