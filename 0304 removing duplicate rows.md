# Tutorial: Removing Duplicate Rows in a Spreadsheet
https://developers.google.com/apps-script/reference/spreadsheet/sheet#getRange(Integer,Integer,Integer,Integer)
```javascript
function removeDuplicates() {
  var sheet = SpreadsheetApp.getActiveSheet();
  var data = sheet.getDataRange().getValues();
  var newData = [];
  for (var i in data) {
    var row = data[i];
    var duplicate = false;
    for (var j in newData) {
      if (row.join() == newData[j].join()) { 
        duplicate = true;
      }
    }
    if (!duplicate) {
      newData.push(row);
    }
  }
  sheet.clearContents();
  sheet.getRange(1, 1, newData.length, newData[0].length).setValues(newData);
}
```
### javascript
- .join()
: 배열의 모든 요소를 연결해 하나의 문자열로 만듦
- .push()
: 배열의 끝에 하나 이상의 요소를 추가하고, 배열의 새로운 길이를 반환함.
### Apps script
- .getRange(row, column, numRows, numColumns) 
: Returns the range with the top left cell at the given coordinates with the given number of rows and columns.
- .setValues(values)
: Sets a rectangular grid of values (must match dimensions of this range).

## Variation
In the example above, the script finds a duplicate when there are two identical rows, but you may also want to remove rows with matching data in just one or two of the columns. To do that, you can change the conditional statement.

### from
```javascript
if(row.join() == newData[j].join()){
  duplicate = true;
}
``` 
### to
```javascript
if(row[0] == newData[j][0] && row[1] == newData[j][1]){
  duplicate = true;
}
```
This conditional statement finds duplicates each time two rows have the same data in the first and the second column of the sheet.

## Reuse the method
There are other solutions to avoid duplicates. For example, you can tag each item as ‘already processed’ once they have been copied. An example can be found in this tutorial: Sending emails from a Spreadsheet. Or, you can remove the item from the list of items to copy.
https://developers.google.com/apps-script/articles/sending_emails#section2
```javascript
// This constant is written in column C for rows for which an email
// has been sent successfully.
var EMAIL_SENT = 'EMAIL_SENT';

/**
 * Sends non-duplicate emails with data from the current spreadsheet.
 */
function sendEmails2() {
  var sheet = SpreadsheetApp.getActiveSheet();
  var startRow = 2; // First row of data to process
  var numRows = 2; // Number of rows to process
  // Fetch the range of cells A2:B3
  var dataRange = sheet.getRange(startRow, 1, numRows, 3);
  // Fetch values for each row in the Range.
  var data = dataRange.getValues();
  for (var i = 0; i < data.length; ++i) {
    var row = data[i];
    var emailAddress = row[0]; // First column
    var message = row[1]; // Second column
    var emailSent = row[2]; // Third column
    if (emailSent !== EMAIL_SENT) { // Prevents sending duplicates
      var subject = 'Sending emails from a Spreadsheet';
      MailApp.sendEmail(emailAddress, subject, message);
      sheet.getRange(startRow + i, 3).setValue(EMAIL_SENT);
      // Make sure the cell is updated right away in case the script is interrupted
      SpreadsheetApp.flush();
    }
  }
}
```
- .flush() : This function forces the code to wait for all changes to be made before executing the rest of the code.
  - Spreadsheet operations are sometimes bundled together to improve performance, such as when doing multiple calls to Range.getValue(). However, sometimes you may want to make sure that all pending changes are made right away, for instance to show users data as a script is executing.
