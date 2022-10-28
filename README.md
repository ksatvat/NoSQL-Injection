# NoSQL-Injection


## NoSQL Injection: MongoDB

### Abstract
Constructing a dynamic MongoDB query with input coming from an untrusted source could allow an attacker to modify the statement's meaning.
### Explanation
NoSQL injection in MongoDB errors occur when:

1. Data enters a program from an untrusted source.



2. The data is used to dynamically construct a MongoDB query.

Example 1: The following code dynamically constructs and executes a MongoDB query that searches for an email with a specific ID.

```
...
    userName = req.field('userName')
    emailId = req.field('emaiId')
    results = db.emails.find({"$where", "this.owner == \"" + userName + "\" && this.emailId == \"" + emailId + "\""});
...
```

The query intends to execute the following code:

```
    this.owner == "<userName>" && this.emailId == "<emailId>"
```

However, because the query is constructed dynamically by concatenating a constant base query string and a user input string, the query only behaves correctly if emailId does not contain a double-quote character. If an attacker with the user name wiley enters the string 123" || "4" != "5 for emailId, then the query becomes the following:

```
    this.owner == "wiley" && this.emailId == "123" || "4" != "5"
```

The addition of the || "4" != "5" condition causes the where clause to always evaluate to true, so the query returns all entries stored in the emails collection, regardless of the email owner.
