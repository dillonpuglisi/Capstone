
# CS-499-X2150 Computer Science Capstone 20EW2 for Dillon Puglisi


## Self-Assessment and ePortfolio

Word document can be found [here](https://github.com/dillonpuglisi/Capstone/blob/gh-pages/Final%20Project%20ePortfolio%20and%20Self-Assessment.docx).


## Code Review

Code review video can be found [here](https://www.youtube.com/watch?v=yP_9FClqQ1s).

## ePortfolio

### Enhancement 3: Database

```markdown

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
