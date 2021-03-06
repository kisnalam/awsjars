
#Problem : How to access various aws resources from a jupyter notebook.
#### Look at Screenshot1,Screenshot2,Screenshot3 to have a visual look at the output from a jupyter notebook invoked using Spark as a service on IBM bluemix.


Add the following jars to your notebook at minumum for below example to work:
```
##Step1:

%AddJar https://raw.githubusercontent.com/kisnalam/awsjars/master/aws-java-sdk-1.10.62.jar -f
%AddJar https://raw.githubusercontent.com/kisnalam/awsjars/master/commons-logging-1.1.3.jar -f
%AddJar https://raw.githubusercontent.com/kisnalam/awsjars/master/jackson-core-2.5.3.jar -f
%AddJar https://raw.githubusercontent.com/kisnalam/awsjars/master/jackson-annotations-2.5.0.jar -f
%AddJar https://raw.githubusercontent.com/kisnalam/awsjars/master/jackson-databind-2.5.3.jar -f
%AddJar https://raw.githubusercontent.com/kisnalam/awsjars/master/joda-time-2.8.1.jar -f
%AddJar https://raw.githubusercontent.com/kisnalam/awsjars/master/httpclient-4.3.6.jar -f
%AddJar https://raw.githubusercontent.com/kisnalam/awsjars/master/httpcore-4.3.3.jar -f

##Step2:

Add the following imports to your jupyter cell:

import java.io.BufferedReader
import java.io.File
import java.io.FileOutputStream
import java.io.IOException
import java.io.InputStream
import java.io.InputStreamReader
import java.io.OutputStreamWriter
import java.io.Writer
import java.util.UUID
import com.amazonaws.AmazonClientException
import com.amazonaws.AmazonServiceException
import com.amazonaws.auth.AWSCredentials
import com.amazonaws.auth.profile.ProfileCredentialsProvider
import com.amazonaws.regions.Region
import com.amazonaws.regions.Regions
import com.amazonaws.services.s3.AmazonS3
import com.amazonaws.services.s3.AmazonS3Client
import com.amazonaws.services.s3.model.Bucket
import com.amazonaws.services.s3.model.GetObjectRequest
import com.amazonaws.services.s3.model.ListObjectsRequest
import com.amazonaws.services.s3.model.ObjectListing
import com.amazonaws.services.s3.model.PutObjectRequest
import com.amazonaws.services.s3.model.S3Object
import com.amazonaws.services.s3.model.S3ObjectSummary
import scala.collection.JavaConversions._
import scala.util.control.Breaks._
import com.amazonaws.auth.BasicAWSCredentials

##Step3 :

Paste the below code in to the jupyter cell and change the Access keys :

  val AWS_ACCESS_KEY_ID = "<Access key>"
  val AWS_SECRET_ACCESS_KEY = "<Access secret>" 
  val credentials = new BasicAWSCredentials(AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY)
  val s3 = new AmazonS3Client(credentials)
  val usEast1 = Region.getRegion(Regions.US_EAST_1)
  println(usEast1)
  val bucketName = "fromjupyter2"
  val key = "MyObjectKeyjupyter2"
  s3.setRegion(usEast1)
  s3.createBucket(bucketName)
  val file = File.createTempFile("aws-scala", ".txt")
        val writer = new OutputStreamWriter(new FileOutputStream(file))
        writer.write("abcdefghijklmnopqrstuvwxyz\n")
        writer.write("01234567890112345678901234\n")
        writer.write("!@#$%^&*()-=[]{};':',.<>/?\n")
        writer.write("01234567890112345678901234\n")
        writer.write("abcdefghijklmnopqrstuvwxyz\n")
        writer.close()
 println(file)
  for (bucket <- s3.listBuckets()) {
        println(" - " + bucket.getName)
      }
  println("Uploading a new object to S3 from a file\n")
  s3.putObject(new PutObjectRequest(bucketName, key, file))
  println("Downloading a existing object from S3\n")
  val `object` = s3.getObject(new GetObjectRequest(bucketName, key))
  println("Content-Type: "  + `object`.getObjectMetadata().getContentType())
  val reader = new BufferedReader(new InputStreamReader(`object`.getObjectContent()))
        breakable {
        while (true) {
            val line = reader.readLine()
            if (line == null)
              break
            println("    " + line)
        }
        }
####NOTE: Depending on the code changes you might need to add other imports and Jars to yout notebook.

```
