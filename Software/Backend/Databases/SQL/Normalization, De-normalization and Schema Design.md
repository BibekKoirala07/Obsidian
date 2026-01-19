### Normalization, and Schema Design
Let's talk about Normalization first.
Good Database can't protect bad data. But there are some case of bad data that some good database design can protect. Like this value can't be logically be true. Like one customer with 2 DOB is logically not possible. It is called failure of data integrity. Like data can't be trusted because it disagree with itself. 

Normalization is like safety assessment.

Let's say we want to write the height of beatle band members in order. We may write with their name in only one column. For us, it represent the order of height of beatle band member. But does database understand it ? No. Even if there was a way to put it, then it shouldn't be done. Rather make two column with one being Name of member and another being Height ranking or Height. ![[Pasted image 20251231205742.png]]Another way of violating 1 NF is having different data types in same column. In fact DB platform won't let us do it anyway. Another way of violating 1 NF is designing table without Primary Key. If a table don't have PM, then that table doesn't fulfil 1 NF. Another way is to repeating group. Let's say we have online game with this database design. ![[Pasted image 20251231210402.png]] What do you think is wrong ? Repeating group of data items on a single row violates 1 NF. What could be right ? Well, we could make the DB with `players_id` and `item1(shields)`, `item 2(arrow)`, `item 3` and so on but imagine what if we have more than 100 items. Do you think it is practical ? Well no. Instead do this. ![[Pasted image 20251231210812.png]]But you may be thinking what is PM key here ? Well it is combination of `Player_ID` and `Item_Type`.

![[Pasted image 20251231210943.png]]

Now let's move forward. Now in that above figure. What if every player has levels like Beginner, Intermediate and so on ? We need to add column with name `level` and write level of each player. ![[Pasted image 20251231211157.png]] Now look ? Isn't `Player_Rating` duplicated ? What if player `gila19` looses all `copper coins` ? She will be deleted from table right ? If we try to query for player `gila19` player rating is ? We can't find it. This problem is known as ==Deletion Anamoly==.
![[Pasted image 20251231211746.png]]Now what if one player with two or more `item type` updates his player rating but somehow something goes wrong and only one row is updated ? Well now data is inconsistent and by itself wrong ? It is called ==Update Anamoly==. 

And What if new player joins the game but don't have any `item type` ? Well he/she couldn't be in database as `item type` is also PM with `player id`. Well it is called ==Insertion Anamoly==.

2 NF is about how table's non key column(attributes) connects to PMK. ==Each non key attribute must depend on the entire primary key==. In our example, `player rating` doesn't depends on PM but only on `player Id`. 

![[Pasted image 20251231213012.png]]The arrow signifies the dependency(==Functional Dependency==). It tells the each value of the thing on left side of arrow is associate with exactly one value of thing on right side of arrow. Like each value of player id and item type is associated with specific item quantity.  But there is something wrong with another dependency. Player rating isn't dependent on PMK but only one part of PMK. 

To solve this we need to make another table ![[Pasted image 20251231213445.png]]
Suppose we decide to add Player skill level in Player table ranging from 1 to 9. and player `gila19` goes from 3 -> 4 in player skill. ![[Pasted image 20251231213855.png]]
and by some chance, something went wrong when updating
`player_rating`. Now the `player_rating` is Beginner. with player skill level 4. That shouldn't happen. That is design error. You can see 2 NF didn't causes error here cuz no attribute depends only partially here on `player_id`. They are fully dependent on PM K. 

![[Pasted image 20251231214618.png]] Here we can see that Player skill level isn't dependent on Player Id but on Player Rating. And Player Rating is dependent on Player PM K. This type of dependency is called ==transitive dependency==.

==The dependency of non key attribute on another non key attribute== is seen here. So this table is not in ==3 NF==.
So what do we do ? We remove Player rating column from table and make new table. We can cover Boyce-Codd Normal Form at the time of covering 3 NF. 
![[Pasted image 20251231215109.png]]

==In almost all cases, once you normalized table this far, you have normalized it totally==.

We need to go to BCNF (Boyce Cod Normal form)

Let's go to ==4 NF==

And Let's go to ==5 NF==.


And conclusion at last. ![[Pasted image 20251231220541.png]]
What you should write here:

#### 🔹 Normal forms (brief, not textbook)

- Why 3NF exists (update anomalies)
    
- What problems normalization actually solves
    

#### 🔹 When normalization hurts

- Too many joins on hot paths
    
- Read-heavy systems (feeds, dashboards)
    
- Reporting queries
    

#### 🔹 Controlled denormalization

- Duplicated columns for reads
    
- Materialized views
    
- Snapshot tables
### De Normalization
Should we be normalizing all our table all the time ? Hell no, there are some situation when fully normalized table is impractical and undesirable. 

So where does data comes from in our database ? One option is it comes from front end right ? and other option is another database. For this topic, the focus is on other database. 

Let have a scenario. The data from other database get interfaced in our database. 