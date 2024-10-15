# qStudio/PRQL Lesson #2 - Playing with qStudio

Lesson #2 tells how to install qStudio,
download and open the _Property\_in\_Lyme_ SQLite database,
and then start making your own queries.

1. **Install qStudio**

  **On a Mac:** 
    * Download the Mac version of qStudio from:  
      [https://test.richb-hanover.com/downloads/qStudio.zip](https://test.richb-hanover.com/downloads/qStudio.zip)
    * Unzip the result to get the **qStudio.app** application
    * Double-click the application.
      You will see a "unverified developer" (or similar) warning.
      Use **System Preferences -> Privacy and Security** to
      indicate that you trust the application.
      Click "Open anyway" if necessary to open qStudio
  
  **On a Windows machine:**
  
    * Download the **Installer for Windows** from the
      qStudio Downloads page:  
      [https://www.timestored.com/qstudio/download](https://www.timestored.com/qstudio/download)
    * Run the installer in the usual way
    * Using the command line, enter `winget install prqlc`
      to install the PRQL compiler.
    * Open qStudio

2. **Register for a free qStudio license**
  When qStudio starts up, it requires you to register for
  a free account.
  Provide your email address and you will be taken to a page
  that displays a license code (a long string).
  Copy that string and paste it into the qStudio window.
    
3. **Download the "Property in Lyme" SQLite database**
  The latest copy of the database is always available from:
  [https://raw.githubusercontent.com/TaxFairness/TaxFairness/refs/heads/main/Property\_In\_Lyme.sqlite](https://raw.githubusercontent.com/TaxFairness/TaxFairness/refs/heads/main/Property_In_Lyme.sqlite)

4. **Open the "Property in Lyme" SQLite database in qStudio.**
   To do this, drag the database's icon into the qStudio window
   (top left corner, but the bottom half of that pane).

## Playing with qStudio

**qStudio is now running.**
Here are some hints for trying its features.
  
### View the tables

Click any of the names of tables
at the top-left of the window.
The columns and rows appear at the bottom of the window.
Interesting tables are:
    
  * **CleanScrapedData** - a cleaned-up list of every parcel
    in Lyme, gleaned from information
    from the Vision database along with easement
    and frontage information.
  * **GraftonCtyRoD** - all records from Grafton County 
    Register of Deeds back to April 2019.
  * **LymeOldToNew** - data from the (original) 2024 MS-1.
    I will update when we are permitted to view the info.

### Create and edit queries

The basic process is:

  * Create a new file in qStudio (**File -> New File**)
  * Save as `qStudioTest.prql` (".prql" suffix is important)
    using **File -> Save As...**  
    _I recommend you save it to a new folder on the Desktop
    so it's easy to find later._
  * Enter a query. Start simple with `from LymeOldToNew` -
    this statement just displays all the
    rows and columns from the `LymeOldToNew` table.
  * Save the file with **Cmd-S** on Mac; **Ctl-S** on Windows
  * Execute the query (**Cmd-E** / **Ctl-E**)
  * _You may be prompted with a request to install the
    "SQLite driver". Allow it to happen._
  * The result from the qury appears in the bottom "Result" pane.
    If the Result pane is not visible,
    choose **Windows -> Result**
  * Edit the query by typing in the window.
    Save and Execute it as above.

### Enhancing your query

Here are examples taken from the first lesson.
You can experiment with qStudio by modifying the
queries as shown below:

> Note - these examples use the [PRQL](https://prql-lang.org)
> language (pronounced "Prequel"), not SQL.
> The 
> [PRQL Reference Book](https://prql-lang.org/book/)
> gives full details on creating queries.

**Select two columns from the `LymeOldToNew` table,
ignoring the rest.** 
Do this by adding a `select` statement at the bottom.
Note that the `select` statement requires a list
enclosed within `{ ... }` that has column names
("LO\_Location" and "LO\_Owner") separated by ",".

```
from LymeOldToNew
select {
   LO_Location,
   LO_Owner,
}
```

_Here's the way to think about the query above.
The statement `from LymeOldToNew` starts a "pipeline" 
with the contents of that table. 
It passes it to the next statement (`select ...`)
that modifies the table,
by retaining only the two named columns (with all the rows)
that could be passed along to any subsequent statement._

_Note that PRQL doesn't care about line breaks.
A statement can be on one or many lines:
the following is equivalent to the above:_
`select { LO_Location, LO_Owner }`

**Select a couple more columns:**

Add more column names between the `{ ... }`, separated by commas.
Trailing commas are allowed, even if no other column name
appears afterwards.

```
from LymeOldToNew
select {
   LO_Location,
   LO_Owner,
   LO_OldValue,
   LO_NewValue,
   LO_Year,
}
```

_The qStudio result now shows five columns,
the first two plus the three new ones.
The Result pane shows them in the order they are listed
in the query._

**Create a new column named "difference" of the new to old values:**

To do this, add a new line with the column name ("difference")
followed by `=`, and then the calculation.

```
from LymeOldToNew
select {
   LO_Location,
   LO_Owner,
   LO_OldValue,
   LO_NewValue,
   LO_Year,
   difference = LO_NewValue - LO_OldValue,
}
```

_Note that the new "difference" column in the result is
just as "real" as any of the original "LO\_xxx" columns._

_Note too, that the comma at the end of the line
is optional, but recommended because
you can comment out any line (see below) without hassle._

**OK. That's all very interesting, but which property had the biggest change?**
Let's sort by the "difference" field.
Add this line to the end, then re-execute the query:

```
sort {difference}
```

_This sorts the rows by the value in the "difference" column.
To reverse the sort, use `sort { -difference }`._

**Oh, but that shows many different years.**
Let's just look at 2024. Add this line to the end:

```
filter (LO_Year == 2024)
```

_This includes any rows where the LO\_Year is equal (`==`) to 2024.
(The double-equal is required.)
To exclude the year 2024, you can use "not-equal" `!=`.
And the `>`, `>=`, `<`, and `<=` operators work as expected._

**Commenting out lines**

Sometimes you want to temporarily (or permanently)
remove a line or group of lines from a query.
PRQL uses `#` as the comment character.
When it appears, the remainder of that line will be ignored.
There is a keyboard shortcut -
**Cmd-/** (Mac) or **Ctl-/** (Windows) - 
that comments out/uncomments the lines that are selected.

**Further observations**

1. (A bit of "PRQL lingo".)
  PRQL statements "transform" the table they receive
  by modifing it in some way and passing it along to
  the next statement (also called a "transform").
  This is the power feature of PRQL: transforms can be 
  "stacked" to form a pipeline that continally modify the
  data as it passes through to produce a final result.

2. The `filter` statement (the "filter transform")
  could have been placed much earlier in the query.
  In fact, it could have immediately followed the
  initial `from ...`
  That would have removed all the other years prior
  to passing them to the first `select` - and in the end,
  the result would have been identical.
  
3. Similarly, the `sort ...` transform could have been placed
  anywhere in this pipeline. 
  From that point in the pipeline,
  all the rows would have remained in the same order.
  
4. There are a few other PRQL transforms
(`join`, `aggregate`, `take`, `group`) that will be covered
in Lesson #3.
