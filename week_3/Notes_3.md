# WEEK 03 NOTES

E X P L O R I N G   A N D   V A L I D A T I N G   D A T A  

---

O V E R V I E W 

---

to see what is in there in the data

evaluating 

subset 

format 

sort

---

**Exploring Data with Procedures**

---

After you access data, the next step is to make sure you understand it. You can use proc contents to view the descriptor portion of the table, and you can visually scan the table to see the data portion. The tables are often too large for a visual review to be sufficient. 

SAS has several procedures that can help you quickly and easily explore your data. 

- The **print** procedure creates a listing of all rows and columns in the data.
- The **means** procedure calculate simple summary statistics for numeric columns in the data. The default statistics are frequency count, mean, standard deviation, minimum, and maximum. From this report, you can see if there are missing values and you can see the range of values which might indicate invalid values.
- The **univariate** procedure also generate summary statistics, but it includes more detailed statistics related to distribution. The report also includes the five lowest and highest extreme values and their observation numbers.
- The **FREQ** procedure creates a frequency table for each column in the input table. The results include the unique values for the column along with their frequency, percent, and cumulative frequency, and percent. This procedure can help you find problems in the data, like miscoded values or case in consistencies. Let's take a look at these procedures in action.

---

**Demo: Exploring Data with SAS Procedures**

---

Let's use the PRINT MEANS UNIVARIATE and FREQ procedures to explore and validate our data. So we'll start with PROC PRINT. The table we want to look at is the storm_summary SAS data set in the PG1 library. So I'll use the DATA= option and provide the table name. So remember by default PROC PRINT will list all of the rows, but I'd like to include just a subset to get a glance at the first 10 rows. So I'll use the OBS= option and 10. I'll submit that PROC PRINT step, and there's our first 10 rows. It also includes all of the columns and the data. 

```sass
proc print data= pg1.storm_summary (OBS=10);
run;
```

So let's go back to the code, and now I'm going to add a VAR statement to limit the columns included in the report. So I start with the keyword var, and then if I know my column names, well, I can certainly just list them, Season, Name, and so on. 

```sass
proc print data= pg1.storm_summary (OBS=10);
var Season Name ;
run;
```

However, a really great shortcut in SAS Studio allows us to populate those column names by just clicking on them. I'll delete Season and Name and ensure that my cursor is in the place where I want to insert the column names. And then I'll come over to the Library's panel. In the PG1 library, I'll expand the STORM_SUMMARY table, and I can see all of the columns listed here. If I hold down my Control key, I can select the **columns in the order** I want to add them to the program. So I'll choose Season holding down the Control key, Name, Basin, MaxWindMPH, MinPressure, StartDate, and EndDate. And then I can simply click and drag into my program, and wherever my cursor was those columns are added in the order that I selected them and end the statement with a semi-colon. 

To document my program, I'll also add a comment before the step to explain what we're doing. List first 10 rows, and I'll highlight that text and **press Control slash**. Let's run that step. And we have 10 rows, but we've now limited the columns and changed the order in the report. So remember the point of these procedures is to validate our data. 

So as we look at the values, what can we observe? We notice that there are some missing values for name. We can see the case inconsistencies with Basin, lowercase na and upper case. We also have some missing values for MaxWindMPH and MinPressure. So that's interesting to observe in just these first 10 rows.

 Well, what else can we do as we validate our data? We can use other procedures. 

I'll come back to the code, copy the PROC PRINT, and below it paste it. But this time I'm going to change the procedure. I'll change print to means. I'd like to compute summary statistics. And I'd like the entire table to be analyzed, so I'll remove the OBS= option. The VAR statement in PROC MEANS lists the columns that we want to analyze. Specifically, I want to analyze MaxWindMPH and MinPressure, so I'll delete the other columns. **The columns on the VAR statement have to be numeric**. I'll add a comment to explain this step as well, and I'll run the PROC MEANS.

```sass
proc means data= pg1.storm_summary ;
var MaxWindMPH MinPressure ;
run;
```

So here we can look at basic summary statistics for these two numeric columns.

- We have a frequency count. The fact that these numbers are different means that there are quite a few missing values for MinPressure compared to MaxWindMPH.
- Specifically, I might want to look at the Minimum and Maximum to see if those ranges appear valid. Six is a pretty small value for MaxWindMPH.
- We might want to investigate further to see if that's accurate. And negative 9,999 is definitely not valid for MinPressure, so we may want to consider later on making those values invalid or missing.

All right, let's try another procedure. Back to the program. I'll copy and paste the PROC MEANS, and this time change means to univariate. PROC UNIVARIATE also computes summary statistics for us, but it also gives us the 5 extreme low and high values. I still want to analyze the same numeric columns on the VAR statement, MaxWindMPH and MinPressure. So I'll add a comment for this step and run it.

```sass
proc UNIVARIATE data= pg1.storm_summary ;
var MaxWindMPH MinPressure ;
run;
```

Although the output for PROC UNIVARIATE includes several other statistics, I'm going to scroll down to concentrate on the Extreme Observations table.-

- So here we can see the observation number and the value for the low and high MaxWindMPH values. We'll probably be interested later on in learning which storm had a maximum wind speed of 213 miles per hour. Scrolling down a bit further, we have the report for MinPressure, and we can see it looks like we have only two rows where the value for MinPressure was negative 9,999. So that gives us a little more insight as to what we're dealing with and the table that we're working with. Finally, let's go back to the code and add one more procedure.

This time we'll do PROC FREQ, and instead of a VAR statement, we're going to use a TABLE statement. And on the TABLE statement, I want to list the columns for which I want to generate One-way Frequency reports, Basin, Type, and Season. I'll add a comment, and we'll submit this last step.

```sass
proc FREQ data= pg1.storm_summary ;
var Basin Type Season ;
run;
```

This is probably my favorite procedure for examining the unique values of a column. 

- We noticed in PROC PRINT that we saw we had lower case values for Basin, specifically for na.
- The Frequency report indicates we have 16 rows in the entire data set that have that lowercase na value. We'll need to correct that later on.
- The values for Type all look valid.
- We don't have any case inconsistencies or incorrect values.
- Finally in the season table, it's interesting just to see how many storms there were per season. We can identify those seasons that maybe had the most storms, such as 2005 with 102.

So this is a great start. These procedures give us insight to our data to start to learn about the values that we have, what we may need to fix as we prepare our data, and it also helps us think about questions we may want to answer as we further analyze our data.

---

**Filtering Rows with the WHERE Statement** 

---

What if you want to filter the rows that appear in a PROC PRINT report? Or what if you only want to calculate summary statistics for a subset of the data based on a condition? 

You can use the powerful and flexible WHERE statement to subset your data. The WHERE statement can be used in PROC PRINT, MEANS, FREQ, UNIVARIATE and many others. 

The WHERE statement consists of the keyword WHERE followed by one or more expressions. An expression tests the value of a column against a condition that you specify. 

```sass
proc print data= pg1.storm_summary ;
	<where expression> ;
	run;

```

First, let's look at expressions that use basic operators. 

- We can use any of these operators to compare the value of a column to a value that you specify. The expression evaluates as true or false for each row. You can use either the symbol or letters to represent these operators in an expression.
- When you specify a value, keep in mind that **character values are case sensitive and must be enclosed in double or single quotation marks**.
- Numeric values are not enclosed in quotation marks and must be standard numeric values.
- In other words, **you cannot include special symbols such as commas or dollar signs.**
- What about comparing the value of a column to a date? Remember that dates are stored as numeric values, so the expression is evaluated based on a numeric comparison. If you want to compare a date column to a fixed date, you type the date using a particular notation, and SAS turns the string you provide into the numeric equivalent to evaluate the expression. **This notation is called the SAS date constant, and it has this format: a one- or two-digit day, a three-letter month, and a two- or four-digit year, enclosed in quotation marks, followed by the letter D**.

---

**Combining Expressions in a WHERE Statement**

---

You can combine multiple expressions with the keywords AND or OR in a WHERE statement. 

```sass
proc print data=sashelp.cars
	var Make Model Type MSRP MPS_City ;
	where Type="SUV" and MSRP <= 30000;
run;
```

In this example code, any rows that meet these two conditions are included in the PROC PRINT results. In this example, the OR keyword is used to provide multiple values. Notice that each condition has to include TYPE=. 

This can be tedious if there are several valid values that you want to include. A more efficient approach in this scenario is to use the IN operator to compare to a list of values. 

```sass
proc print data=sashelp.cars
	var Make Model Type MSRP MPS_City ;
	where Type="SUV" or Type="Truck" or Type="Wagon";
run;

*/ABOVE CODE CAN BE WRITTEN IN A SIMPLER WAY /*
proc print data=sashelp.cars
	var Make Model Type MSRP MPS_City ;
	where Type in ("SUV" "Truck" "Wagon");
run;
```

After typing the column name, you use the keyword IN, and in parentheses, list the values separated by commas or spaces. The IN operator works with both numeric and character values. Just keep in mind that character values are case sensitive and must be enclosed in quotation marks. You can also take advantage of the **keyword NOT** to reverse the logic of the IN operator.

---

**Demo: Filtering Rows with Basic Operators**

---

In this demo we'll write several different WHERE expressions to filter the data in the Storm Summary table. I'll start by completing the PROC PRINT statement to read the Storm Summary table.

```sass
proc print data=pg1.storm_summary'
	where MaxWindMPH >= 156 ;
run;
```

So let's say we would like to print only those rows where MaxWindMPH is greater than 156. This actually represents Category 5 storms. So I'll start by typing a WHERE keyword and then the column name MaxWindMPH and greater than or equal to and just the numeric constant 156. Because it is a numeric constant we don't quote the value. I'll run that program. And in our output, we can see that all of the values for MaxWindMPH are greater than or equal to 156. **Another helpful thing to point out is in the log, it will tell us exactly how many observations or rows met the criteria that was provided in the WHERE statement.** 

Let's go back to the program. This time I'll change the WHERE statement. Let's say I only want to look at rows where Basin is West Pacific, or the code WP. I'll change the WHERE expression to name the variable Basin and just use equal and then quote capital "WP" quote. 

```sass
proc print data=pg1.storm_summary'
	where Basin in ('SI' 'WP' );
run;
```

A couple of things to remember with the character constant. 

- First, the value does need to be quoted. Either double or single quotes are fine.
- The other critical thing to remember is that character values and quotes are case sensitive. So we will only return those rows where Basin is capital WP.

 I'll run this program and take a look at the column for Basin. 

What if I want to print rows from more than one Basin? The easiest way to do that is to use the IN operator. So where Basin in and then in parentheses I'll list the different Basins. The first one will be SI for South Indian and the second one will be NI for North Indian. Again, remember the case sensitivity and quotes. 

**You can separate the values with either a space or a comma.** And in the results, we can see Basin is SI. If I look in the log, it does indicate that there were 672 observations where Basin was either NI or SI.

What about filtering on dates? Remember this is where we need to use the SAS date constant. 

So I'd like to include all of those rows where StartDate is on or after January 1st, 2010. So I'll use the greater than or equal to operator and then provide the SAS date constant. So in quotes, "I provide the date in the form 01 numeric day, the three letter month JAN, and the two or four digit year, 2010." Close the quote and follow with the letter d. 

```sass
proc print data=pg1.storm_summary'
	where StartDate >= '01jan2010'd ;
run;
```

So the SAS date constant will be converted to the number of days from January 1st, 1960 in order to evaluate our WHERE expression. I'll submit the program and it looks like our StartDates are all after January 1st, 2010. 

What about a compound expression? We can do that as well. So in my WHERE statement I would like to include those rows where Type is equal to capital TS for a tropical storm and HEM_EW. So hemisphere east west is equal to a capital W. The keyword AND means that both of these conditions must be true in order for a row to be printed. 

```sass
proc print data=pg1.storm_summary'
	where MaxWindMPH >= 156  and Type='TS';
run;
```

We'll run that program and Type is TS and HEM_EW is W. Looks great. 

How about using the OR keyword in an expression? Let's say we want all those rows where MaxWindMPH is greater than 156. I'll change AND to OR. And the second condition will be MinPressure less than 920. Let's run this program and take a look at the results.

Remember with the OR keyword only one of the expressions needs to be true for a row to be included in the output. So let's take a look at this first row. MaxWindMPH certainly isn't greater than 156, but MinPressure is missing. Remember the expression was for MinPressure to be less than 920. Missing values are technically treated as the smallest possible value. So when MinPressure is missing, the expression MinPressure less than 920 is true. So all of these rows with MinPressure as missing are included in the report. 

```sass
proc print data=pg1.storm_summary'
	where MaxWindMPH >= 156  or MinPressure < 920;
run;
```

**What if we'd like to exclude these missing values for MinPressure? Well, we can certainly do that back in our WHERE statement. In the expression for MinPressure I could add that MinPressure should also be greater than 0 as well as less than 920. So let's run that program.**

```sass
 proc print data=pg1.storm_summary'
	where MaxWindMPH >= 156  or 0< MinPressure < 920;
run;
```

And notice for MaxWind is greater than 156 or **MinPressure is between 0 and 920.** 

---

**Using Special WHERE Operators**

---

Let's talk about some special WHERE operators that you can use in expressions. Suppose you want to filter your data by missing values. 

```sass
where <column name> = <'what we wnat to search like . - or just space too'>;
where Type= ' ' or Typr=.
```

You could write an expression where a column is equal to a period for a numeric missing value or a space enclosed in quotation marks for a character missing value, but a simpler option is to use the IS MISSING or IS NOT MISSING special operator.

```sass
where <column name> is missing;
where Age is missing;
Where Name is not missing;
```

This code can be used for either numeric or character missing values. 

If you happen to be working with data coming from a DBMS environment that distinguishes between missing and null values, **there is also an IS NULL operator**. 

The BETWEEN AND operator is handy for numeric and character ranges. The endpoints of the range are inclusive.

```sass
where <col name> BETWEEN value1 AND vlaue2 ;
where Age between 20 and 39;
```

**Finally, the LIKE operator enables us to do pattern matching. The percent symbol is a wildcard for any number of characters and the underscore is a wildcard for a single character.** 

```sass
where <col name> like'value';
where City like 'New%';

*/returns New York, New Delhi Newport Newcastle New /*
```

In this example, **the percent sign after New returns any string after New including spaces and letters.** 

```sass
where City like 'Sant_ %';
*/returns Santa Clara, Santa Cruz, Santo Domingo/*
```

In this example, **the underscore represents a single character and then a space and percent sign returns both Santa and Santo, a space, and any other string.**

---

**Sorting Data**

---

Sorting data can be a helpful and sometimes necessary step in exploring your data. 

to sort on groups or measures so that you can visually examine the high or low values. 

as a way to identify and remove duplicate rows. 

```bash
proc sort data=input_table <output=output_table>;
	by <descending> col_name(s);
run;
```

Using PROC SORT, you can sort on one or more character or numeric columns. Similar to other procedures, you use the DATA= option to specify the input table. Although it's optional, we often use the OUT= option to specify an output table because if you don't include the OUT= option, PROC SORT changes the order of the rows in the input table. Every PROC SORT step must include a BY statement. The BY statement specifies one or more columns in the input table whose values are used to sort the rows. The BY statement also indicates whether you want to sort in ascending or descending order. By default, SAS sorts in ascending order. If you want to sort by a column in descending order, you must specify the DESCENDING keyword immediately before each column that you want in descending order. The columns you list on the BY statement are sometimes called BY columns or BY variables. PROC SORT doesn't generate printed output, so you have to open or print the sorted table if you want to look at it. This BY statement sorts the data by ascending values of Name. This BY statement sorts first by ascending values of Name, and then within Name by ascending values of TestScore. And finally this BY statement sorts by ascending values of Subject, and then within Subject by descending values of TestScore.

---

**Identifying and Removing Duplicates**

---

By adding options to the PROC SORT statement, you can identify and remove duplicates in your data. You can use the NODUPKEY option to remove adjacent duplicated values for the columns you specify on the BY statement. 

```sass
proc sort data=input-table <OUT=output-table>
		NODUPEKEY < DUPOUT=output-table>;
	BY col-name(s);
run;

proc sort data=input-table <OUT=output-table>
		NODUPEKEY < DUPOUT=output-table>;
	BY col-name(s);
run;

```

When you add the NODUPKEY option to the PROC SORT statement, only the first occurrence for each unique value of the column listed in the BY statement is kept in the output table. Suppose you want to find and remove entirely duplicated rows, In other words, remove rows that are next to each other in the data where the values for every column match.

```sass
proc sort data=input-table <OUT=output-table>
		NODUPEKEY < DUPOUT=output-table>;
	BY _ALL_;
run;
```

 You can use the keyword *ALL* in the BY statement instead of a column name. This sorts the data by all columns so that entirely duplicated rows are adjacent, and then the NODUPKEY option can do its job. 

**The table listed in the OUT= option is the new table without duplicates.** 

**The table listed on the DUPOUT= option contains the duplicate rows that were removed by NODUPKEY.** 

Using the DUPOUT= option can be helpful for validation. 

Here's an example of using the NODUPKEY option to remove entirely duplicated rows. 

```sass
proc sort data=pg1.class_test3
			out=test_clean
			nodupkey
			dupout=test_dups;
	by _all_;
run;
```

You can see that the input table class_test3 has two rows for Barbara that contain identical information. 

The output table test_clean has only one of the duplicate rows and the output table test_dups has the row that was removed.

If you want to identify and remove duplicate values for specific columns, you specify those columns in the BY statement. Then, NODUPKEY keeps only the first occurrence for each unique value of the column listed in the BY statement in the output table. 

```sass
proc sort data=pg1.class_test3
			out=test_clean
			nodupkey
			dupout=test_dups;
	by Name;
run;
```

Let's see how this works in an example. In this example, we keep only the first row for each unique value of Name in class_test2. Because the math test was listed first for each student in the data, the output table test_clean has all the math test information, and the test_dups table has the reading test information.

---

**Demo: Identifying and Removing Duplicate Values**

---

Let's use the NODUPKEY option in PROC SORT to identify and remove duplicates. We'll start by looking at the Storm_Detail SAS table. Recall that this is a table that includes multiple rows per storm. Notice we have measurements every six hours. It's possible that there may be some rows that are entirely duplicated. And in that situation, we would like to remove those duplicates. We would also like to filter this data to create a table that has the minimum value of pressure for each individual storm. Each storm is uniquely identified by the season, basin, and name. It's possible that names are reused in basins or seasons. So by identifying each unique storm, we can keep only the first row within each storm, the one that has the minimum pressure, and output that information to a new table. We can use PROC SORT to accomplish both tasks.

```sass
proc sort data=pg1.storm_details out=strom_clean nodupkey dupout=storm_dups;
	by _all_ ;
run;
```

In the firt step you can see that we're reading storm_detail and creating a sorted copy that will be named storm_clean. What I like to do with this code is modify it so that we remove any entirely duplicated rows. The first modification I'll make is to add a BY statement. To ensure that entirely duplicated rows are adjacent, I need to sort by all columns in the data. And the shortcut to do that is to use the keyword _ALL_.With the data arranged with duplicate rows adjacent, I can then use the NODUPKEY option on the PROC SORT statement. Only the first row, if there are duplicates, will be kept in storm_detail. I'll use the DUPOUT= option so that any duplicate rows that are removed will be written to a table named storm_dups. I'll run this PROC SORT step

In the Storm_Clean table, looks like we are left with 50,757 rows. And in Storm_Dups, we have seven rows that were removed from the original Storm_Detail table. 

For my second objective, I'd like to create a table that includes one row per storm that captures the minimum pressure. In the second PROC SORT step, we're filtering for non-missing values of Name and Pressure. And then I'm sorting by descending Season, then Basin, Name, and Pressure. Based on this sort sequence, the first row for each Season, Basin, and Name will have the minimum value of Pressure. Next, I'll modify the third PROC SORT statement in order to sort the minimum pressure table from the previous PROC SORT step.

I want to group the data by descending Season, then Basin, and Name.  By using the NODUPKEY option on the PROC SORT statement, we will only keep the first row for each combination of those columns listed on the BY statement. 

I'll run these last two PROC SORT steps.

```bash
*step 1
proc sort data=pg1.storm_details out=strom_clean nodupkey dupout=storm_dups;
	by _all_ ;
run;

*step 2
proc sort data=pg1.storm_details out-min_pressure;
	where Pressure is not missing and Name is not missing;
	by descending Season Name Pressure;
run;

*step 3
proc sort data=min_pressure nodupkey;
	by descending Season Basin Name;
run;
```

And as we take a look at the results, we can see we have one row for storm Agatha with the minimum pressure, as well as all of the other storms included in the original Storm_Detail table.

[WEEK%2003%20NOTES%200785ba53df7d41ca83edc07146533747/sas_week03.pdf](WEEK%2003%20NOTES%200785ba53df7d41ca83edc07146533747/sas_week03.pdf)
