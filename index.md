
# CS-499-X2150 Computer Science Capstone 20EW2 for Dillon Puglisi


## Self-Assessment and ePortfolio

Word document can be found [here](https://github.com/dillonpuglisi/Capstone/blob/gh-pages/Final%20Project%20ePortfolio%20and%20Self-Assessment.docx).

![GitHub Logo](https://github.com/dillonpuglisi/Capstone/blob/gh-pages/Capture2.JPG)

## Code Review

Code review video can be found [here](https://www.youtube.com/watch?v=yP_9FClqQ1s).

## Code Snippets

### Enhancement 1: Software Design and Engineering

BASIC INFO

Full code files and supporting screenshots can be found [here](https://github.com/dillonpuglisi/Capstone/blob/gh-pages/Milestone%20Two%20code%20files.zip).

```javascript
//11/15/20 9:06a-9:09a
//take in inputs from form, attempt to create note record
function AddNote_Submit() {
    var obj = {};
    obj.Title = $("#AddNote_TitleInput").val();
    obj.Note = $("#AddNote_NotesInput").val();
    obj.Encrypt = $("#AddNote_EncryptInput").is(':checked');
    if (obj.Encrypt == true)
	obj.Password = $("#PasswordInput").val();
    else
	obj.Password = "";
    //run condenced external function that submits AJAX call
    RunFunction("AddNote_Submit()", obj, "Notes.aspx/AddNoteReturnID", ["startsWith", "Error"],
	function () {
	    //CloseModal("AddEditNoteForm");
	    //UpdateNotesTable();
	    //$("#EditNote").css('border', '2pt solid green'); //highlight the edit textarea in green to show successful update
	    // $("#UpdateDateLbl").html(GetDate2());   //update the "Last Edited" timestamp displayed
	    window.location.href = "Notes.aspx?noteid=" + data.d; //redirect to edit page for that note record
	}, null);
}
```
BASIC INFO

```C#
//11/15/20 9:12a-9:15a
[WebMethod]
//take in note info (title, note content, whether or not it should be encrypted and a password), and create Notes record; return either Error OR NEW ID
public static String AddNoteReturnID(String title, Boolean encrypt, String password, String note)
{
    //check integrity of inputs
    if (MyString.IsBlank(title))
	return "Error: missing note title";
    if (MyString.IsBlank(note))
	return "Error: missing note content";
    if (encrypt && MyString.IsBlank(password))
	return "Error: cannot encrypt without password";

    try //wrapped in try/catch
    {
	//instantiate new SqlCommand with insert query
	string sqlstring = "INSERT INTO Notes (Title,DateCreated, Note,Active, encrypted) VALUES (@Title,@DateCreated,@Note,'true',@encrypted);SELECT SCOPE_IDENTITY() AS newID;";
	SqlConnection con = new SqlConnection(System.Configuration.ConfigurationManager.ConnectionStrings["DP_DB"].ConnectionString);
	SqlCommand cmd = new SqlCommand(sqlstring, con);
	//attach parameter values to command obj
	cmd.Parameters.AddWithValue("@Title", title);
	cmd.Parameters.AddWithValue("@DateCreated", MyNet.GetEasternDateTime());
	cmd.Parameters.AddWithValue("@Note", (encrypt ? StringCipher.Encrypt(note, password) : note)); //if note should be encrypted per user input, encrypt it using given password
	con.Open();
	System.Data.SqlClient.SqlDataReader reader = cmd.ExecuteReader(); //need to execute as Reader so the new ID can be retrieved
	String newID = "-2";
	while (reader.Read())
	    newID = reader["newID"].ToString();

	con.Close();
	if (newID.Contains("-")) //if read() loop never run, return failure message
	    return "Error: no new ID returned: " + newID;
	return newID;
    }
    catch (Exception exc)
    {
       // MyException.HandleException(DateTime.Now, System.Reflection.MethodBase.GetCurrentMethod().Name, "", exc);
	return "Error: " + exc.Message + "; " + exc.StackTrace;
    }
}
```	
	

### Enhancement 2: Algorithms and Data Structure

BASIC INFO

Full code files and supporting screenshots can be found [here](https://github.com/dillonpuglisi/Capstone/blob/gh-pages/Milestone%20Three%20code%20files.zip).

```C#
//11/22/20 9:27a-9:44a
function BuildFieldCustomizations(fields) {
    output = "";
    console.log("fields: ", fields);
    //output += "//" + table;
    output += "//" + AppendToListItems(fields, "", "", ",") + "\n\n";
    output += "<table>";
    output += "<thead><tr>";
    output += "<th>Field</th>";
    output += "<th>isPk</th>";
    output += "<th>isEditable</th>";
    output += "<th>Datatype</th>";
    output += "</tr>";
    for (var i = 0; i < fields.length; i++) { //loop through each item in array
        output += "<tr>";
        output += "<th>" + fields[i] + "</th>";
        if (i == 0)
            checked = " checked ";
        else
            checked = "";
        output += "<td><input type='radio' name='SQLScriptFieldCustomizations_isPKRadio' id='SQLScriptFieldCustomizations_isPKRadio" + i + "' value='" + i + "' " + checked + "/></td>";
        output += "<td><input type='checkbox' id='SQLScriptFieldCustomizations_isEditableCheck" + i + "' value='" + i + "' checked='checked'/></td>";
        output += "<td><select id='SQLScriptFieldCustomizations_DatatypeInput" + i + "' onchange=\"SQLScriptFieldCustomizations_DatatypeInputChanged('" + i + "')\">" +
            "<option value='Text'>Text</option>" +
            "<option value='Number'>Number</option>" +
            "<option value='Bit'>Bit/Bool</option>" +
            "<option value='Date'>Date</option>" +
            "<option value='DateTime'>DateTime</option>" +
            "</select>" + 
            "<div id='FieldOptions_Text" + i + "'>" + 
                "Char Limit: <input type='number' id='FieldOptions_TextCharLimit" + i + "' style='width:55px;'/>  " + 
            "</div>" + 
            "<div id='FieldOptions_Number" + i + "' style='display:none;'>" +
                "Step (1, 0.1, 0.01, etc): <input type='number' id='FieldOptions_NumberStep" + i + "' style='width:55px;'/>  " +
                "Min: <input type='number' id='FieldOptions_NumberMin" + i + "' style='width:55px;'/>  " +
                "Max: <input type='number' id='FieldOptions_NumberMax" + i + "' style='width:55px;'/>  " + 
            "</div>" + 
            "<div id='FieldOptions_Bit" + i + "' style='display:none;'>" + 
                "Default: <input type='checkbox' id='FieldOptions_BitDefault" + i + "' />  " + 
            "</div>" + 
            "<div id='FieldOptions_Date" + i + "' style='display:none;'>" + //doesn't currently need any additional fields
            "</div>" + 
            "<div id='FieldOptions_DateTime" + i + "' style='display:none;'>" + //doesn't currently need any additional fields
            "</div>" + 
            "</td>";
        output += "</tr>";
    }
    output += "</thead>";
    output += "</table>";

    return output;

}
```

### Enhancement 3: Database

BASIC INFO

Full code files and supporting screenshots can be found [here](https://github.com/dillonpuglisi/Capstone/blob/gh-pages/Milestone%20Four%20code%20files.zip).

```MySQL

-- =============================================
-- Author:		DP
-- Create date: 11/23/20 -9:46a
-- Description:	executed upon insert/update/delete into Notes table, stores audit log in separate table including before/after values
-- =============================================
ALTER TRIGGER [dbo].[Notes_LogAudit] on [dbo].[Notes] 
   AFTER INSERT,DELETE,UPDATE
AS 
BEGIN
	-- SET NOCOUNT ON added to prevent extra result sets from
	-- interfering with SELECT statements.
	SET NOCOUNT ON;

	--determine what operation was run
	--REF: https://stackoverflow.com/questions/741414/insert-update-trigger-how-to-determine-if-insert-or-update
	DECLARE @Operation as nvarchar(50);
    SET @Operation = (
		CASE WHEN EXISTS(SELECT * FROM INSERTED) AND EXISTS(SELECT * FROM DELETED)
			THEN 'Update'  
		WHEN EXISTS(SELECT * FROM INSERTED)
			THEN 'Insert' 
		WHEN EXISTS(SELECT * FROM DELETED)
			THEN 'Delete'  
		ELSE NULL -- ie failed deletion operation?   
    END)

	--get before/after values from inserted/deleted tables
	declare @NoteID1 int 
	declare @NoteID2 int 
	declare @Title1 nvarchar(MAX) --before update/delete 
	declare @Title2 nvarchar(MAX) --after update/insert

	declare @Note1 nvarchar(MAX) --before update/delete 
	declare @Note2 nvarchar(MAX) --after update/insert

	select @NoteID1=NoteID, @Title1=Title, @Note1=Note from inserted
	select @NoteID2=NoteID, @Title2=Title, @Note2=Note from deleted

	--insert log into audit table
    insert into Notes_AuditLog (NoteID_ins, NoteID_del, timestamp, Operation, Title_ins, Title_del, Note_ins, Note_del) 
		VALUES (@NoteID1, @NoteID2, GETDATE(),@Operation, @Title1, @Title2, @Note1, @Note2 )
		
END
```

============================================================================================


I can use the [editor on GitHub](https://github.com/dillonpuglisi/Capstone/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever I commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in my site, from the content in my Markdown files.

Page is accessible through this [link](https://dillonpuglisi.github.io/Capstone/).

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling my writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/dillonpuglisi/Capstone/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
