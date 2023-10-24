To list accounts/list roles/assume role using sso:
```
 $ aws configure sso --profile default --endpoint-url  --yes
 
 $ aws sso login (redirects to browser and sets ~/.aws/

 $ cat ~/.aws/sso/cache/* | jq -s '.[] | select(.startUrl == "https://mainremitly.awsapps.com/start") | .accessToken'

 $ aws --region us-west-2 sso --profile sso list-accounts --access-token $(cat ~/.aws/sso/cache/* | jq -s '.[] | select(.startUrl == "https://msz23.awsapps.com/start#/") | .accessToken' | tr -d '"')

 $ aws --region us-west-2 sso --profile sso list-account-roles --account-id 12345567890 --access-token ...

 $ aws --region us-west-2 sso --profile sso get-role-credentials --account-id 12345567890 --role-name RoleName --access-token ...

 $ OUT=$(aws --region us-west-2 sso --profile sso get-role-credentials --account-id 12345567890 --role-name RoleName --access-token ...);\
   export AWS_ACCESS_KEY_ID=$(echo $OUT | jq -r '.Credentials''.AccessKeyId');\
   export AWS_SECRET_ACCESS_KEY=$(echo $OUT | jq -r '.Credentials''.SecretAccessKey');\
   export AWS_SESSION_TOKEN=$(echo $OUT | jq -r '.Credentials''.SessionToken');

 $ aws sts get-caller-identity
 ```

What role was assumed can be inspected using CloudInsights:
```
filter (eventName =~ /Assume/ and requestParameters.principalTags.companylogin =~ /szlagor/)
|fields @timestamp, requestParameters.principalTags.companylogin, requestParameters.durationSeconds, responseElements.assumedRoleUser.arn
| sort @timestamp desc
```
