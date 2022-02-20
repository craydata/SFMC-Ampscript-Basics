# Retrieving a Salesforce fields and poplating a form with Ampscript

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
