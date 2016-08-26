##  SQL Injection
SQL Injection, or SQLi for short, is a type of web application security vulnerability that exploits the database layer through rogue code injection techniques. SQL Injection attacks can occur when a web application utilizes user-supplied data without proper validation or encoding as part of a command or query. This leaves an opening for potential hacks or attacks as specially crafted user data can trick the application into executing unintended commands or changing data. These commands are then sent to the database server where they are executed. SQLi attacks are easily preventable, yet most companies don’t take precautions, subjecting themselves to potentially damaging data breaches and their consequences.

**How Attackers Exploit SQLi Vulnerabilities**

SQLi flaws in software code leave a web application open to hacks and attacks. As one of the most common security vulnerabilities found in software, SQLi flaws are often the common entry point for any number of malicious schemes. Here’s how SQLi attacks generally work:
- SQLi hacks take advantage of an unprotected application’s acceptance of user- supplied data without proper validation or encoding controls.
- In one common scenario, a hacker hijacks the application’s unprotected login fields to gain unauthorized access to its backend database.
- An attacker may also exploit SQLi by submitting a bogus SQL command to the database, confusing the SQL interpreter with specially crafted input data.
- The SQL interpreter is not able to distinguish between intended commands and code injected by the attacker, which is then executed, exposing the database.
- The attacker can then trick the application into extracting all data records or hosting a malicious script, among other aims.
In summary, SQL Injection allows an unauthorized third-party to create, read, update, alter or delete data accessed via the unprotected application.

### Defenses
It is very simple to avoid SQL Injection vulnerabilities in your code. These five fixes either avoid the use of dynamic SQL queries entirely or prevent user-supplied input from affecting query logic. They can be used with practically any kind of programming language and any type of database.

- **Use prepared statements (aka parameterized queries)**. Simpler to write and easier to understand than dynamic queries, parameterized queries pass in each parameter to the query layer only after all SQL code has been defined. The database can distinguish between code and data, regardless of user input. An attacker isn’t able to change the intent of a query, even if SQL commands are inserted.
- **Perform Input Validation**. Authenticate user input against a set of defined rules for length, type and syntax as well as against business rules as an additional query filter.
- Escape all user supplied input, before putting it in a query. This is a cost-effective way to retrofit legacy code and avoid performance issues, but an inferior approach when writing new apps. Every DBMS supports one or more character escaping schemes specific to certain kinds of queries, so use them. The DBMS will not confuse user input with SQL code written by the developer, thus avoiding any possible SQLi.
- **Enforce least privilege**. Minimize the privileges assigned to database accounts in your environment. Start with no access rights and then determine case-by-case what each requires. Some users will be fine with read-only access or limited views of the data. Restrict an account’s ability to execute stored procedures. Create specific accounts application by application. Rarely, if ever, grant the ability to create or delete access to database accounts. Minimize privileges granted to the application itself, to reduce the likelihood of unauthorized access.
- **Use stored procedures**. This fix stores all SQL code in the database itself, then calls it from the application. SQL code is defined first, and then parameters are passed after. Avoid generating dynamic SQL inside stored procedures. If this can’t be avoided, then validate or properly escape all user supplied input to the dynamic query before it’s constructed. Remove any and all stored procedures that are not in use.

### Vulnerable Code View
```
dvna.get('/users', function (req, res) {
  db.get('SELECT * FROM users WHERE `login`="frank"' + (req.query.order || ''), function (err, row) { // <-- BOOM, yummy accounts.
    res.send(row.login + ': ' + row.password);
    res.end();
  });
})
```

### Patched Code
```
app.get('/:name', function(request, response) {
  // validate that the name only has letters if not return a 400 error
  if (request.params.name.match(/[^a-zA-Z]/)) {
    response.sendStatus(400);
    return;
  }
  
  connection.query('SELECT * FROM accounts WHERE name="' + request.params.name + '"',
    function(err, rows, fields) {
      if (err) {
        next(err);
        return;
      }
      response.send(JSON.stringify(rows));
    });
});
```

### Exploit Code
```
// construct the URL with this
/";SELECT%20*%20FROM%20accounts%20WHERE%20"1"="1
```