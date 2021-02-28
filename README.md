<h1 align="center"> RAZOR PAYMENT GATEWAY INTEGRATION IN YM CHATBOT </h1>
<h2 align="center"> ( USING CODE IN DEVELOPER SECTION ) </h2>

In this document, we will learn about how to integrate the razor payment gateway into a <a href="https://yellowmessenger.com/" target="_blank">YM</a> ChatBot.

**Step 1:** Go to Razor Payment Website and create an Account (https://razorpay.com/).

**Step 2:** Login to your account and Go to the Dashboard of RazorPay.

**Step 3:** By Default, you will be in Test Mode but make sure you are in it. We would be using __Test Mode__ as of now.

NOTE: There are 2 modes in Razor Payment Gateway:

* __Test Mode:__ This mode is for testing and for developers who are just getting started in Razor Payment Integration. Test Mode is completely Free of Charge. No real charges will be made, even if we give our valid card details in the Razor Payment Gateway Page.

* __Live Mode:__ As the name suggests, In the Live Mode, we would be charged for real. In order to work on Live Mode, we have to give our bank account and KYC Details.
<br/>
<img width="1440" alt="Screenshot 2021-02-24 at 9 45 11 AM" src="https://user-images.githubusercontent.com/66553883/109414390-9d118500-79d8-11eb-85cf-605b3be5175b.png">

**Step 4:** In the Dashboard, Go to __Settings__ and click on __API Keys__ Tab. Generate a New Key. Click on the __Download Key Details__ and save the key details in a safe location. Click Ok.

NOTE:

__Only__ when you generate a new API key for the __first time__, you can see the __Key Secret__ value. So make sure to save the key details in a safe accessible place.

<br/>
<img width="1440" alt="Screenshot 2021-02-24 at 10 24 04 AM" src="https://user-images.githubusercontent.com/66553883/109415077-9127c200-79dc-11eb-8843-c7c06e5e1974.png">

**Step 5:** For the API request of Razorpay Payment, we have to use __Basic Auth Authorization__.

To get the Basic Auth Authorization value, we are gonna use the __POSTMAN__(v8.0.6) application.  

1. Open POSTMAN application, click on __New -> Collection__ and create a collection with a name.

2. Click on __New -> Request__. Give a Request Name and save it to the collection which we created above.

3. Click on __Authorization__ Tab, click __Type__ and select __Basic Auth__.

4. Open the downloaded file which contains your API keys.

5. Give __Key Id in Username__ Textbox and __Key Secret in Password__ Textbox.

<br/>
<img width="1440" alt="Screenshot 2021-02-26 at 10 54 39 PM" src="https://user-images.githubusercontent.com/66553883/109415302-e2848100-79dd-11eb-9a43-d319eb5c3960.png">

6. After giving the values in the username and password textboxes, go to __Headers__ tab.

7. If No Headers key value pairs are visible, Click on the hidden eye option and you would see all the key value pairs in the Headers.

8. Click on the __value of the Authorization__ key and Copy it.

<br/>
<img width="1440" alt="Screenshot 2021-02-26 at 11 12 14 PM" src="https://user-images.githubusercontent.com/66553883/109415441-be756f80-79de-11eb-922b-f1f70c2cd463.png">

**Step 6:** Now let's go to the Developer Section of our ChatBot in the YM Platform. 

NOTE:

The Recommended Step to Integrate a Payment Gateway is after the Step in which we get the cart details of the products which users want to buy from us. 

	-> After the Successful Payment, we can store the cart details in our DataBase.

**Step 7:** Let’s create a New Function for the Razor Payment Gateway Integration API Code.

**Step 8:** We are going to use a POST request an API call to the Razor Payment Gateway with the required Details as below :
```node js
	let dt = new Date();
	let max = 10000, min = 100;
 
	let totalPrice = 500;
	let botId = app.bot;
	//gives the ID of your bot
 
	let config = {
	   method: 'POST',
	   url: 'https://api.razorpay.com/v1/invoices',
	   headers = {
	       'Authorization': 'Basic cnpwX3Rlc3RfNE4zeGZtTUTJcUkyQk46d3ZKQ3VodkFDa3VFaFlITjFkRnM2R09Q',
	       'Content-Type': 'application/json'
	   },
	   data: {
	       "customer": { //optional ; we can sent these details of user in this customer object
	       "name":”XYZ”,
	       "email":”xyz@xyz.com”,
	       "contact": 9678954326
	   },
	   "type": "link", //MANDATORY
	   "view_less": 1, //optional
	   "amount": totalPrice, //MANDATORY ; amount value should be in the smallest unit of the given currency value ; For INR, it should be ‘paise’
	   "currency": "INR",//MANDATORY
	   "description": "YM Restaurant Orders Payment",//MANDATORY
	   "receipt": "Receipt No : " + Math.floor(Math.random() * (max - min + 1) + min), //optional ; to display receipt string in the payment gateway
	   "sms_notify": 1, //optional ; 0 - No, 1- Yes ; If you want to send the Payment link via SMS
	   "email_notify": 1, //optional ;  0 - No, 1- Yes; If you want to send the Payment Link via EMAIL
	   "expire_by": Math.round(dt.getTime() / 1000), //optional; link expiry time
	   "notes": { //MANDATORY ; SENDER,SOURCE AND BOT KEY ARE MANDATORY ! From these 3 keys, we are able to identify whom and from which bot is making the razor payment ! If we want to send any additional information, we use this notes object
	       "sender": app.sender,
	       "source": app.source,
	       "bot": botId
	       // "bot": "x5189765732960"
	   }
	   }
	};

	let response = await app.axios(config);
	//app.log(response.data, "THIS IS POST AXIOS data !");
	//app.log(response.status,"THIS IS POST AXIOS STATUS !");
	//app.log(response.statusText, "THIS IS POST AXIOS statusText !");
	if (response.data.short_url) {
	   //app.log(response.data.short_url, "THIS IS response.data.short_url !");
	   await app.sendTextMessage("PAYMENT LINK: ");
	   await app.sendTextMessage(response.data.short_url);
	   return resolve();
	}
	return resolve({
	   success: false,
	   question: "THERE ARE SOME TECHNICAL ISSUES ! KINDLY TRY AFTER SOME TIME !"
	});
```

**BRIEF EXPLANATION:**
 
1. We are creating an object with __method type__, __url__, __headers__ and __data__. 
2. Then, We are creating the required key value pairs (i.e) __Authorization Value__ and Content-Type for the __headers__ object.
3. Then, We are passing the required data in the key value pair format to the data object.
4. Then, We are passing that object which contains all the above information to the __app.axios()__ method and we are storing this result in a variable. 

NOTE:
 
* To know about axios, go through this link : https://blog.logrocket.com/how-to-make-http-requests-like-a-pro-with-axios/   
* Our YM Platform __does not support__ Shorthand methods for Axios HTTP requests such as axios.get(), axios.post() etc.
