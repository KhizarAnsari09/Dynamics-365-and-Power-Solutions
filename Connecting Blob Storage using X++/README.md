<pre>
<code>
using Microsoft.Azure.Storage.Auth.StorageCredentials;
using Microsoft.Azure.Storage.CloudStorageAccount;
using Microsoft.Azure.Storage.Blob.CloudBlobClient;
using Microsoft.Azure.Storage.Blob.CloudBlobContainer;
using Microsoft.Azure.Storage.Blob.CloudBlobDirectory;

using System.IO.MemoryStream;
using System.IO.StreamReader;

public final class FetchDataFromBlobStorage
{
    public static void main(Args _args)
    {
        Microsoft.Azure.Storage.Auth.StorageCredentials storageCredentials = new Microsoft.Azure.Storage.Auth.StorageCredentials("&lt;Storage_Name&gt;", "&lt;Blob_Connection_String&gt;");

        Microsoft.Azure.Storage.CloudStorageAccount cloudStorageAccount = new Microsoft.Azure.Storage.CloudStorageAccount(storageCredentials, true);

        Microsoft.Azure.Storage.Blob.CloudBlobClient cloudBlobClient =  new Microsoft.Azure.Storage.Blob.CloudBlobClient(cloudStorageAccount.BlobStorageUri, storageCredentials, null);

        Microsoft.Azure.Storage.Blob.CloudBlobContainer cloudBlobContainer = cloudBlobClient.GetContainerReference("&lt;Container_Name&gt;");

        Microsoft.Azure.Storage.Blob.CloudBlobDirectory cloudBlobDirectory = cloudBlobContainer.GetDirectoryReference("&lt;Folder_Name&gt;");

        var listOfBlobs = cloudBlobDirectory.ListBlobs(true, 0, null, null);

        var enumerator = listOfBlobs.GetEnumerator();

        while(enumerator.MoveNext())
        {
            Microsoft.Azure.Storage.Blob.CloudBlockBlob cloudBlockBlob = enumerator.get_current();

            str blobTier = cloudBlockBlob.Properties.StandardBlobTier.ToString();
            
            if(blobTier != "Archive")
            {
                System.IO.MemoryStream memStream = new System.IO.MemoryStream();

                cloudBlockBlob.DownloadToStream(memStream, null, null, null);

                memStream.Seek(0, System.IO.SeekOrigin::Begin);

                System.IO.StreamReader reader = new System.IO.StreamReader(memStream);

                str blobContent = reader.ReadToEnd();

                Info(strFmt("Blob Name: %1, Blob Tier: %2 Blob Content: %3", cloudBlockBlob.Name, blobTier, blobContent));
                
                memStream.SetLength(0);

                memStream.Dispose();
            }
        }
    }
}
</code>
</pre>
