# Cloud Storage
Create bucket
```
export BUCKET_NAME_1=itbrain1311
echo $BUCKET_NAME_1
```
Run the following command to download a sample file (this sample file is a publicly available Hadoop documentation HTML file):

```
curl http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/ClusterSetup.html > setup.html
```
To make copies of the file, run the following commands:

```
cp setup.html setup2.html
cp setup.html setup3.html
```

### Copy the file to the bucket and configure the access control list
1 . Run the following command to copy the first file to the bucket:

```
gsutil cp setup.html gs://$BUCKET_NAME_1/
```

2. To get the default access list that's been assigned to setup.html, run the following command:
```
gsutil acl get gs://$BUCKET_NAME_1/setup.html  > acl.txt
cat acl.txt
```
3. To set the access list to private and verify the results, run the following commands:

```
gsutil acl set private gs://$BUCKET_NAME_1/setup.html
gsutil acl get gs://$BUCKET_NAME_1/setup.html  > acl2.txt
cat acl2.txt
```

4. To update the access list to make the file publicly readable, run the following commands:

```
gsutil acl ch -u AllUsers:R gs://$BUCKET_NAME_1/setup.html
gsutil acl get gs://$BUCKET_NAME_1/setup.html  > acl3.txt
cat acl3.txt
```

### Examine the file in the Cloud Console

### Delete the local file and copy back from Cloud Storage

2. Run the following command to delete the setup file:
```
rm setup.html
```

4. To copy the file from the bucket again, run the following command:

```
gsutil cp gs://$BUCKET_NAME_1/setup.html setup.html
```

### Customer-supplied encryption keys (CSEK)

#### Generate a CSEK key

For the next step, you need an AES-256 base-64 key.

1. Run the following command to create a key:

```
python3 -c 'import base64; import os; print(base64.encodebytes(os.urandom(32)))'

```
Copy the value of the generated key excluding b' and \n' from the command output. Key should be in form of

#### Modify the boto file

The encryption controls are contained in a gsutil configuration file named .boto.

1. To view and open the boto file, run the following commands:

```
ls -al

nano .boto
```
2. Locate the line with "#encryption_key="

3. Uncomment the line by removing the # character, and paste the key you generated earlier at the end.

4. Press Ctrl+O, ENTER to save the boto file, and then press Ctrl+X to exit nano.


### Upload the remaining setup files (encrypted) and verify in the Cloud Console

1. To upload the remaining setup.html files, run the following commands:

```
gsutil cp setup2.html gs://$BUCKET_NAME_1/
gsutil cp setup3.html gs://$BUCKET_NAME_1/
```

### Delete local files, copy new files, and verify encryption

1. To delete your local files, run the following command in Cloud Shell:

```
rm setup*

```

2. To copy the files from the bucket again, run the following command:

```
gsutil cp gs://$BUCKET_NAME_1/setup* ./
```

3. To cat the encrypted files to see whether they made it back, run the following commands:

```
cat setup.html
cat setup2.html
cat setup3.html
```

## Rotate CSEK keys

### Move the current CSEK encrypt key to decrypt key

1. Run the following command to open the .boto file:

```
nano .boto
```
2. Comment out the current encryption_key line by adding the # character to the beginning of the line.

3. Uncomment decryption_key1 by removing the # character, and copy the current key from the encryption_key line to the decryption_key1 line.

4. Press Ctrl+O, ENTER to save the boto file, and then press Ctrl+X to exit nano.

### Generate another CSEK key and add to the boto file

1. Run the following command to generate a new key:

```
python3 -c 'import base64; import os; print(base64.encodebytes(os.urandom(32)))'
```
2. Copy the value of the generated key excluding b' and \n' from the command output. Key should be in form of tmxElCaabWvJqR7uXEWQF39DhWTcDvChzuCmpHe6sb0=.

3. To open the boto file, run the following command:
```
nano .boto
```
4. Uncomment encryption and paste the new key value for encryption_key=.

5. Press Ctrl+O, ENTER to save the boto file, and then press Ctrl+X to exit nano.

### Rewrite the key for file 1 and comment out the old decrypt key

When a file is encrypted, rewriting the file decrypts it using the decryption_key1 that you previously set, and encrypts the file with the new encryption_key.

You are rewriting the key for setup2.html, but not for setup3.html, so that you can see what happens if you don't rotate the keys properly.

1. Run the following command:
```
gsutil rewrite -k gs://$BUCKET_NAME_1/setup2.html
```
2. To open the boto file, run the following command:

```
nano .boto
```

3. Comment out the current decryption_key1 line by adding the # character back in.

4. Press Ctrl+O, ENTER to save the boto file, and then press Ctrl+X to exit nano.

### Download setup 2 and setup3

1. To download setup2.html, run the following command:

```
gsutil cp  gs://$BUCKET_NAME_1/setup2.html recover2.html
```
2. To download setup3.html, run the following command:

```
gsutil cp  gs://$BUCKET_NAME_1/setup3.html recover3.html

```

## Enable lifecycle management

### View the current lifecycle policy for the bucket

1. Run the following command to view the current lifecycle policy:

```
gsutil lifecycle get gs://$BUCKET_NAME_1

```
#### Create a JSON lifecycle policy file

1. To create a file named life.json, run the following command:

```
nano life.json

```
2. Paste the following value into the life.json file:

```
{
  "rule":
  [
    {
      "action": {"type": "Delete"},
      "condition": {"age": 31}
    }
  ]
}
```

3. Press Ctrl+O, ENTER to save the file, and then press Ctrl+X to exit nano.


### Set the policy and verify

1. To set the policy, run the following command:

```
gsutil lifecycle set life.json gs://$BUCKET_NAME_1

```
2. To verify the policy, run the following command:

```
gsutil lifecycle get gs://$BUCKET_NAME_1
```

## Enable versioning
### View the versioning status for the bucket and enable versioning

1. Run the following command to view the current versioning status for the bucket:

```
gsutil versioning get gs://$BUCKET_NAME_1

```

2. To enable versioning, run the following command:


```
gsutil versioning set on gs://$BUCKET_NAME_1
```

3. To verify that versioning was enabled, run the following command:

```
gsutil versioning get gs://$BUCKET_NAME_1
```

### Create several versions of the sample file in the bucket

1. Check the size of the sample file:

```
ls -al setup.html

```
2. Open the setup.html file:

```
nano setup.html

```
3. Delete any 5 lines from setup.html to change the size of the file.
4. Press Ctrl+O, ENTER to save the file, and then press Ctrl+X to exit nano.


5. Copy the file to the bucket with the -v versioning option:

```
gsutil cp -v setup.html gs://$BUCKET_NAME_1
```

6. Open the setup.html file:

```
nano setup.html
```

7. Delete another 5 lines from setup.html to change the size of the file.
8. Press Ctrl+O, ENTER to save the file, and then press Ctrl+X to exit nano.


9. Copy the file to the bucket with the -v versioning option:

```
gsutil cp -v setup.html gs://$BUCKET_NAME_1
```
### List all versions of the file

1. To list all versions of the file, run the following command:

```
gsutil ls -a gs://$BUCKET_NAME_1/setup.html
```
2. Highlight and copy the name of the oldest version of the file (the first listed), referred to as [VERSION_NAME] in the next step.
``Make sure to copy the full path of the file, starting with gs://``
3. Store the version value in the environment variable [VERSION_NAME].

```
export VERSION_NAME=v1.1
```
4. Verify it with echo:

```
echo $VERSION_NAME

```
### Download the oldest, original version of the file and verify recovery

1. Download the original version of the file:
```
gsutil cp $VERSION_NAME recovered.txt

```
2. To verify recovery, run the following commands:

```
ls -al setup.html

ls -al recovered.txt

```
## Synchronize a directory to a bucket

### Make a nested directory and sync with a bucket

Make a nested directory structure so that you can examine what happens when it is recursively copied to a bucket.


1. Run the following commands:

```
mkdir firstlevel
mkdir ./firstlevel/secondlevel
cp setup.html firstlevel
cp setup.html firstlevel/secondlevel

```

2. To sync the firstlevel directory on the VM with your bucket, run the following command:

```
gsutil rsync -r ./firstlevel gs://$BUCKET_NAME_1/firstlevel
```

4. Compare what you see in the Cloud Console with the results of the following command:

```
gsutil ls -r gs://$BUCKET_NAME_1/firstlevel

```

###  Cross-project sharing

### Switch to the second project

##### SSH to the VM

```
export BUCKET_NAME_2=itbrain1211_2
echo $BUCKET_NAME_2
```
4. Store [FILE_NAME] in an environment variable:

```
export FILE_NAME=it_file1
echo $FILE_NAME
```
6. List the files in [PROJECT_ID_2]:

```
gsutil ls gs://$BUCKET_NAME_2/
```

#### Authorize the VM

5. Enter the following command in the terminal to authorize the VM to use the Google Cloud API:


```
gcloud auth activate-service-account --key-file credentials.json
```
##### Verify access

1. Retry this command:


```
gsutil ls gs://$BUCKET_NAME_2/
```
2. Retry this command:

```
gsutil cat gs://$BUCKET_NAME_2/$FILE_NAME
```
3. Try to copy the credentials file to the bucket:


```
gsutil cp credentials.json gs://$BUCKET_NAME_2/
```

#### Verify changed access

1. Return to the SSH terminal for crossproject.
2. Copy the credentials file to the bucket:

```
gsutil cp credentials.json gs://$BUCKET_NAME_2/
```

