# pork_dbobject
orm framework - study!
Pork.dbObject
WTF is Pork.dbObject?

It's an Object-Relation mapper for PHP 5 that attempts to be easy, fast, lightweight, uses optimized database queries and provides an easy Find() function very loosely based on rails'. Also, i'm coming with Pork.Generator: A PHP5 based database analyzer that automatically sees the different types of relations in your (MySQL) database and creates linked dbObjects for it.

Rrright... And now in English?

In short, the O/R mapper handles all your basic Select/Insert/Update/Delete/Join statements for you so you won't have to write the same SQL over and over. You just create PHP objects that are linked to a row in the database.

Code says more than words: let's consider the following really basic example:
You have these 2 (empty) tables in your favorite database:

Weblogs
ID_Weblog
strTitle
strAuthor
strStory
datPosted
Replies
ID_Reply
ID_Weblog
strAuthor
strReply
datPosted
strIp

As you can see, the Weblogs table has a 1:many relation to the replies table because the ID_Weblog is a field there. Now, let's say you've already created the dbObjects Weblog and Reply, and we want to insert a new row into the Weblogs table.

 <?php     
    $weblog = new Weblog(); // create an empty object to work with. 
    $weblog->Author = 'SchizoDuckie'; // mapped internally to strAuthor. 
    $weblog->Title = 'A test weblog';  
    $weblog->Story = 'This is a test weblog!'; 
    $weblog->Posted = date("Y-m-d H:i:s"); 
    $weblog->Save(); // Checks for any changed values and inserts or updates into DB. 
    echo ($weblog->ID) // outputs: 1 

So that's one record created! Now let's add 2 replies:

 <?php 
    $reply = new Reply(); 
    $reply->Author = 'Some random guy'; 
    $reply->Reply = 'w000t'; 
    $reply->Posted = date("Y-m-d H:i:s"); 
    $reply->IP = '127.0.0.1'; 
    $reply->Connect($weblog); // auto-saves $reply and connects it to $weblog->ID  

    $reply2 = new Reply(); 
    $reply2->Author = 'Some other random guy'; 
    $reply2->Reply = 't000t'; 
    $reply2->Posted = date("Y-m-d H:i:s"); 
    $reply2->IP = '127.0.0.1'; 
     
    $weblog->Connect($reply2); // the other way around works too. 
    } 
Now, we have 3 records in the datababase. One in Weblogs and 2 in Replies.
Now let's say you want to pull weblog #1 from the database, and display it plus it's replies
 <?php 

    $weblog = new Weblog(1); //Fetches the row with primary key 1 form table weblogs and hooks it's values into $weblog;

    echo("<h1>{$weblog->Title}</h1> 
    <h3>Posted by {$weblog->Author} @ {$weblog->Posted}</h3> 
    <div class='weblogpost'>{$weblog->Story}</div>"); 

    // now fetch the connected posts. this is the real magic: 
    $replies = $weblog->Find("Reply"); // fetches a pre-filled array of Reply objects. 
    if ($replies != false) 
    { 
        foreach($replies as $reply) 
        { 
            echo("<div class='weblogreply'><h4>By {$reply->Author} @ {$reply->Posted}</h4> {$reply->Reply}</div>"); 
        } 
     
And that's it! You've just displayed your weblog!
For more elaborate examples check out the Examples section.

Sounds cool! Will i need to configure loads of XML to get started?

Well... that's the popular way, but I personally think people are going too far with this seperating logic from configuration stuff. I've solved this by creating a one-line __setupDatabase() function for Pork.dbObject that you call inside your own constructor and contains only the neccesary information. After you've done this, you can simply create a relation to another dbObject by calling
$this->addRelation(string ClassName [, string connectorClassName]);
For more info check the Usage section or head over to the Examples.

What databases does that support?

It's already been field-tested with MySQL, Ms SQL Server, SQLite, ODBC and Oracle. It should actually work with about any type of database that supports returning the last rowId after inserting one, but for now you'll have to settle for MySQL.

Will Pork.dbObject force me to create my tables in some funky way?

Pork.dbObject will force you to create your databases the RIGHT and descriptive way. Just create unique integer auto-increment primary keys for your tables and Pork.dbObject will know what to do.

Does Pork.dbObject force me to create 5 levels of directories before I can even start?

Hell no! Do things the way you were used to for all that I care... I w
