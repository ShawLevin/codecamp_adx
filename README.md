# codecamp_adx
Code Camp 2023 ADX Workshop

## Agenda
* Introductions / Survey
* AWS Account Setup

![image](https://github.com/ShawLevin/codecamp_adx/assets/4535134/d2672f92-9e77-44ef-8d83-f49256504289)


![image](https://github.com/ShawLevin/codecamp_adx/assets/4535134/040572e4-bd8d-4b70-a9cc-3184ce6139d7)

![image](https://github.com/ShawLevin/codecamp_adx/assets/4535134/cd911087-8a52-48d7-8726-a8c2987e8a84)

![image](https://github.com/ShawLevin/codecamp_adx/assets/4535134/0944fde5-12e3-4f9a-b5cf-93c57d17d54a)

![image](https://github.com/ShawLevin/codecamp_adx/assets/4535134/1e9817a3-20f0-4e2e-9957-bd682dfb7a62)

https://us-east-1.console.aws.amazon.com/dataexchange/home?region=us-east-1#/products/prodview-e2aizdzkos266
https://us-east-2.console.aws.amazon.com/dataexchange/home?region=us-east-2#/products/prodview-maizvnithsspe

```
using Amazon;
using Amazon.Runtime;
using Amazon.Runtime.CredentialManagement;
using Amazon.S3;
using Amazon.S3.Model;

Console.WriteLine("Hello, Code Camp!");

AWSCredentials smh = new Amazon.Runtime.BasicAWSCredentials("ACCESS_KEY", "SECRET"); 
{
    using (var client = new AmazonS3Client(smh, RegionEndpoint.USEast1))
    {
        
        var response = client.ListBucketsAsync().Result;
        Console.WriteLine(response.HttpStatusCode);

        //S3 Bucket
        ListObjectsV2Request listObjReq = new ListObjectsV2Request() { BucketName = "shaw-codecamp-demo"};
        var objects = client.ListObjectsV2Async(listObjReq).Result;
        foreach(var o in objects.S3Objects)
        {
            Console.WriteLine(o.Key);
            Console.WriteLine(DownloadObjectFromBucketAsync(client, o.BucketName,o.Key,"s3downloads/"+o.Key).Result.ToString());
        }

        //Access Point
        //Discuss requester pays and egress fees.
        ListObjectsV2Request listObjApReq = new ListObjectsV2Request() { BucketName = "arn:aws:s3:us-east-1:540564263739:accesspoint/418d6540-4b8e-42f5-bf4a-d38add280333",RequestPayer=RequestPayer.Requester};
        var apObjects = client.ListObjectsV2Async(listObjApReq).Result;
        foreach(var o in apObjects.S3Objects)
        {
            Console.WriteLine(o.Key);
        }


    }    
}
         static async Task<bool> DownloadObjectFromBucketAsync(
            IAmazonS3 client,
            string bucketName,
            string objectName,
            string filePath)
        {
            // Create a GetObject request
            var request = new GetObjectRequest
            {
                BucketName = bucketName,
                Key = objectName,
            };

            // Issue request and remember to dispose of the response
            using GetObjectResponse response = await client.GetObjectAsync(request);

            try
            {
                // Save object to local file
                await response.WriteResponseStreamToFileAsync($"{filePath}\\{objectName}", true, CancellationToken.None);
                return response.HttpStatusCode == System.Net.HttpStatusCode.OK;
            }
            catch (AmazonS3Exception ex)
            {
                Console.WriteLine($"Error saving {objectName}: {ex.Message}");
                return false;
            }
        }

```
