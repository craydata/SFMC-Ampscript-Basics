# Retrieving Salesforce fields and poplating a form with Ampscript

```
%%[
	SET @Id = QueryParameter('id')
	SET @Rows = RetrieveSalesforceObjects('Contact', 'FName, LName', 'CustomerID', '=', @Id)
	IF ROWCOUNT(@Rows) > 0 THEN
	SET @FirstName = FIELD(ROW(@Rows,ROWCOUNT(@Rows)),'FName')
	SET @LastName = FIELD(ROW(@Rows,ROWCOUNT(@Rows)),'LName')
	ENDIF
]%%

<form>
	<input name="fname" value="%%=v(@FirstName)=%%" type="text" placeholder="First name">
	<input name="lname" value="%%=v(@LastName)=%%" type="text" placeholder="Last name">
	<button>Submit</button>
</form>
```
## Ampscript in a form

```
%%[

SET @formHandlerPageId = 7788
SET @action = RequestParameter("action")
SET @pageUrl = RequestParameter('PAGEURL')

IF IndexOf(@pageUrl,'?') > 0 THEN
    SET @pageUrl = Substring(@pageUrl,0,Subtract(IndexOf(@pageUrl,'?'),1))
ENDIF

]%%
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Subscription Form</title>
    <style>
        .wrapper {
            width:600px;
            min-height: 300px;
            margin: 5% auto 0;
        }
    </style>
</head>
<body>
    <div class="wrapper">
        <h1>Newsletter subscription.</h1>
        %%[ IF @action == "confirm" THEN ]%%
            <p>Please check your email.</p>
        %%[ ELSEIF @action == "success" THEN ]%%
            <p>Thank you for subscribing.</p>
            <p><a href="%%=v(@pageUrl)=%%">Go back</a></p>
        %%[ ELSEIF @action == "error" THEN ]%%
            <p>Your token is missing or has expired.</p>
            <p><a href="%%=v(@pageUrl)=%%">Please try again</a></p>
        %%[ ELSEIF @action == "exist" THEN ]%%
            <p>Your are already subscribed.</p>
            <p><a href="%%=v(@pageUrl)=%%">Go back</a></p>
        %%[ ELSE ]%%
            <form method="POST" action="%%=CloudPagesURL(@formHandlerPageId)=%%">
                <label for="Email">Your email:</label><br>
                <input name="email" type="email" placeholder="ex: jon.snow@winterfell.co.uk">
                <br>
                <button>Subscribe me</button>
            </form>
        %%[ ENDIF ]%%
    </div>
</body>
</html>
```

# How to retrieve records using complex filters with server-side Javascript

```
<script runat="server">
    Platform.Load("core","1.1.1");
	var DE = DataExtension.Init("0000-1111-2222-4444-6666");
	var complexFilter = {
		LeftOperand: {
			Property: "Adult", 
			SimpleOperator: "equals", 
			Value: true 
		},
        LogicalOperator: "OR",
        RightOperand: {
			LeftOperand: {
				Property: "Age", 
				SimpleOperator: "isNotNull"
			},
			LogicalOperator: "AND",
			RightOperand: {
				Property: "Age", 
				SimpleOperator: "greaterThan", 
				Value: 18 
			}
		}
	}
	var adults = DE.Rows.Retrieve(complexFilter);
</script>
```

