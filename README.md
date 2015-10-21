# Database Optimizations

Original migration time: ```Completed running in 6044.447746 seconds.```
After indices: ```AddIndices: migrated (0.0000s)``` (HOW?)

Timeline did not work at all for me. I had it open and running for about 30 minutes and it was still buffering at 8%. 

1. Once you have optimized your code as much as you think you can, drop the database, run `rake db:migrate`, and then time how long it takes to run `rake db:seed`.  Was there an improvement or a worsening of runtime?  By what percent and why?
1. Which is faster: (a) running `rake db:seed` without indices and then running a migration to add indices, or (b) adding indices during your initial `rake db:migrate`, then running `rake db:seed`? b

* Size of your database: 563.5 MB
* Record the size of your development log.
* Give at least one method (feel free to Google) for reducing the size of one of these, yet keeping your data intact: number one answer on Stack Overflow was to delete the file. Rails apparently creates a new log file if one doesn't exist. Another popular answer: log rotation.
* Do you think that this is smaller, about right, or larger than the size of databases you'll be working with in your career?
I think this is probably larger, unless I somehow end up at... I don't know, amazon.

Now let's talk about the "memory" numbers given on the page.  What impact have your changes had on memory usage?  If you reload a page again and again (with no code changes in between reloads), does memory used stay the same?  Have you ever been able to make memory used go down? Let's go over this?

#### Part Two - Search Bar

A common feature which you'll be asked to develop is a Google-like search.  You enter information in one field, and results are returned when any one of a number of fields matches what you entered.

Create a new action in your `reports` controller which loads the same data, but with no `:name` parameter.  Call the new action/view/route `search`.  In the view, add a single search field (and search button).  The user should be able to type any part of an assembly's `name`, a hit's `match_gene_name` OR a gene's `gene` field.  When the search button is pressed, the page will reload and the user will be shown all of the hits for which any of those things match.

In other words, if a user types in "special" and one assembly has a `name` "Special Assembly" (and no hits have "special" in their `match_gene_name`), all hits for just that assembly will be shown.  If a user types in "tetanus" and only one hit has a `match_gene_name` which includes "tetanus" (and no assemblies have "tetanus" in their `name`), only that one hit will be shown.  If a user types in "AACCGGTT", only hits for genes with "AACCGGTT" in them should be shown.

The search should also be case insensitive.

## Hard Mode

Improve the intelligence of the search bar.  If you type in multiple words, your search algorithm should split on spaces and display results for which ALL of the terms are found in ANY of the three fields.  For instance, if you search for "Special Tetanus ACTG", a result would still get returned if "Special" was in its assembly, "Tetanus" was in its hit, and "ACTG" was in its gene.

## Nightmare Mode

Back to the `all_data` action.  This data structure has a number of tables connected with a series of one-to-many relationships between them.  A more advanced way to improve efficiency would be to cache the id of the upper-most (ancestor) table's id in a field in the lower-most (descendant) table.  To accomplish this, do the following:

* Write a migration to add this cached foreign key.
* Write callbacks to maintain this foreign key appropriately.  Hint: you will need more than one.
* Modify the report to use this new cached field instead of the actual id stored in the ancestor table.
* Measure the improvement in runtime.
