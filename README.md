# Khanalyze

A solution to the Khan Challenge Project. Compares code against templates.

### Example

Open 'editor.html' to mess around! 

Tested in latest versions of Chrome, Firefox, Safari, and IE.

### API methods
- **setParser(parser,options)**: Sets parser (see method for more info)
- **setCheck(name, template)**: Add or update a check to be performed during .test.
- **removeCheck(name)**
- **removeAllChecks()**
- **test(code)**: Gives feedback on code per each 'check'.

For checks, template can be one of:
- `typename`: ex. 'ForStatement'
- `[typename]`: ex. ['IfStatement', 'VariableDeclaration']
- `ast`: ex. { type:'ForStatement' } (this is equivalent to the first example)
- `[ast]`: ex. \[{ type:'IfStatement' }, { type:'VariableDeclaration' }\]  (same as 2nd example)

Thus the code analyzer can take another AST as a template. Because of the
large number of cases, this functionality is not completely guaranteed
for more complex ASTs.

Sample use case:

```javascript
khanalyze.addCheck('whitelist', 'ForStatement');
var feedback = khanalyze.test(code);
if (feedback !== null) {
  var r = feedback['whitelist'];
  // do something here...
}
  ```

Feedback is given as a dictionary mapping checks to results in the form of **markup**. 
Markup has the following properties:
- **.included**: Returns array of code snippets per each matched template element
- **.missing**: Returns array of unmatched template elements

For instance, if you
```javascript
khanalyze.setCheck('whitelist', ['IfStatement', 'ForStatement']);
``` 

and
```javascript
khanalyze.test('if(true) {}');
```

you'll get:
```javascript
feedback['whitelist'].included = [{ raw:'IfStatement', start:Position, end:Position }]
feedback['whitelist'].missing  = ['ForStatement']
```

where Position is as specified in [Mozilla's Parser API](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/SpiderMonkey/Parser_API):
```
interface Position {
  line: uint32 >= 1;
  column: uint32 >= 0;
}
```

Note that if you want Position information, you must
specify the location option when you set the parser.
For acorn, that would be:
```javascript
khanalyze.setParser(acorn, { location:true });
  ```

### Important Note

In this example, acorn.js has been modified to allow the ellipse '...' (as 'Wildcard' type)
in javascript where possible, so that structural statements like:

```javascript
for (...) { 
  if (...) {}
}
```

can export as ASTs.

### Future Work

The ideal is to have a system that takes this 'forgetful'
structure as its input, so that the designer only has to
outline the required functionality in the same language they
are familiar with. This notational approach also has the benefit of 
carrying variables and functions, for instance:

```javascript
var A = ...
for (...) {
  var B = func(A, ...)
  if (B === ...)
    break;
}
```

where, because func is not defined in the template, 'func'
must be repeated verbatim in the tester's code (e.g. 'onClick'), while
'A' is a stand-in for any variable name which meets the specification.

Note that the current code, due to time limitations, does not evaluate
variable/function stand-ins in the method described above. 

### Credit
- acorn: See LICENSE in acorn directory.
- ace: See LICENSE in ace directory.
- khanalyze by Ian Arawjo

