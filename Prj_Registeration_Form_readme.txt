Scenario 1: 
Client if browses any other path on domain => error.html (S3 Static website)
End

Scenario 2: 
- Client if browses domain => index.html (S3 Static website)
index.html (If accessed http://sre-ashish-assessment.s3-website.ap-south-1.amazonaws.com/)

Site come up with basic HTML: 
=========================
<html>
<head>
    <meta charset="UTF-8">
    <title>Register yourself</title>
</head>
<body>
    <form>
        <label>First Name :</label>
        <input type="text" id="fName">
        <label>Last Name :</label>
        <input type="text" id="lName">
		<label>Email :</label>
        <input type="text" id="cEmail">
        <!-- set button onClick method to call function we defined passing input values as parameters -->
        <button type="button" onclick="callAPI(document.getElementById('fName').value,document.getElementById('lName').value,document.getElementById('cEmail').value)">Signup</button>
    </form>
</body>
=========================
- If submited registeration form. (S3 Static website)
'POST' method sent on API 'http://sre-ashish-assessment.s3-website.ap-south-1.amazonaws.com/' in index.html.
DOM script: 
=========================
   <script>
        // define the callAPI function that takes a first name and last name as parameters
        var callAPI = (firstName,lastName,emailAddress)=>{
            // instantiate a headers object
            var myHeaders = new Headers();
            // add content type header to object
            myHeaders.append("Content-Type", "application/json");
            // using built in JSON utility package turn object to string and store in a variable
            var raw = JSON.stringify({"firstName":firstName,"lastName":lastName,"emailAddress":emailAddress});
            // create a JSON object with parameters for API call and store in a variable
            var requestOptions = {
                method: 'POST',
                headers: myHeaders,
                body: raw,
                redirect: 'follow'
            };
            // make API call with parameters and use promises to get response
            fetch("https://r94nfubvlb.execute-api.ap-south-1.amazonaws.com/dev", requestOptions)
            .then(response => response.text())
            .then(result => alert(JSON.parse(result).body))
            .catch(error => console.log('error', error));
        }
    </script>
=========================

API called "RegisterForm" (API gateway)

Create method => POST => Integrate request with LAMBDA (API gateway)

Configure LAMBDA function to post method which will triger Lambda function "RegisterationForm".(Language used python) (LAMBDA)

Create object in dynamodb use boto3 liberary. (LAMBDA)
dynamodb = boto3.resource('dynamodb')

Select dynamo3 table: 
table = dynamodb.Table('RegisterForm')

Define function to catch event i.e. firstname, lastname and email address. (LAMBDA)

put values in table: (LAMBDA)

    response = table.put_item(
        Item={
            'ID': name,
            'Email': email,
            'RegistrationTime':now
            })


Lambda creates object in DynamoDB table "RegisterForm" with help of method: 'POST' from index.html (DYNAMODB)
i.e. var callAPI = (firstName,lastName,emailAddress) with addition to RegisterationTime when the lambda function was triggered.


Create DynamoDB table "RegisterForm" (DYNAMODB)


Lambda returns statusCode 200 with message "json.dumps('Thank you for showing interest, ' + name)" (LAMBDA)


To return status code: 

    return {
        'statusCode': 200,
        'body': json.dumps('Thank you for showing interest, ' + name)
    }

API returns HTTP Status: 200 back to client. (API Gateway)