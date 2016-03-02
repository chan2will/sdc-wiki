# General
* No `print()` or `console.log()` statements
* VERY SPARINGLY include commented-out code - only if it's a very important note of what to do/not to do
* Delete extra whitespace. 

# Structure
Jade, Sass, and JavaScript files should all follow the same naming and folder structure conventions
```
scc-api/
├── smilecheck/
|   ├── sites/
|   |   ├── staff/
|   |   |   ├── static/
|   |   |   |   ├── staff/
|   |   |   |   |   ├── images/
|   |   |   |   |   |   ├── logo.png
|   |   |   |   |   |   ├── cases/
|   |   |   |   |   |   |   ├──caseHeader.png
|   |   |   |   |   ├── scripts/
|   |   |   |   |   |   ├── cases/
|   |   |   |   |   |   |   ├── detail.js
|   |   |   |   |   |   |   ├── list.js
|   |   |   |   |   ├── styles/
|   |   |   |   |   |   ├── cases/
|   |   |   |   |   |   |   ├── detail.scss
|   |   |   |   |   |   |   ├── list.scss
|   |   |   |   |   |   ├── app.scss
|   |   |   ├── templates/
|   |   |   |   ├── staff/
|   |   |   |   |   ├── cases/
|   |   |   |   |   |   ├── detail.jade
|   |   |   |   |   |   ├── list.jade
|   |   |   |   |   ├── eval_order_wizard/
|   |   |   |   |   |   ├── 1_email.jade
|   |   |   |   |   |   ├── 2_customer.jade
|   |   |   |   |   ├── base.jade
|   |   |   |   |   ├── sidenav.jade
|   |   |   |   |   ├── topnav.jade
```

# Django Templating
* Placement of all `block` and `load` tags should be at the top of the file.
* In Django template code, put one (and only one) space between the curly brackets and the tag contents.
```
//bad
{{foo}}

//good
{{ foo }}
```

For more questions regarding Pythonic Coding styles, reference [Django Coding Styles](https://docs.djangoproject.com/en/1.9/internals/contributing/writing-code/coding-style/)
## Template Tags

# Markup
## HTML/Jade 
For markup, we use [Jade](http://jade-lang.com/), more specifically [PyJade](https://github.com/syrusakbary/pyjade).

* Use Jade syntax in: for loops and if statements - NEVER USE `| {% for x in y %}`
* Use pipe-django (`| {% sometag %}`) for things not built into Jade and blocks
  * `| {% with case.patient.prescription_set.first as rx %} ... {% endwith %}`
  * `| {% block navbar %} ... {% endblock %}`
  * `| {% csrf_token %}`
* For Django context variables use single quotes and Django syntax (`attr='{{ variable_name }}'`)
* For Jade variables (in mixins only) use unquoted variable names (`attribute=variable_name`)
* Except in very special cases, use pipe-django syntax for `include` and `extends`
* Don't use `each` or `while` Jade keywords
* Do not use `- var x = variable` ... use `{% with stuff as var %}.. {% endwith %}`

If you have conditional attributes/classes, do something like this:
```
for object in objects:
  .my-div(class='{{ object.variable|yesno:"active,inactive" }}')
    .insides Content
```

## Styles
* Don't use IDs, use Classes.
* Do not use inline styles. 
* Don't use vendor prefixes, because we use [Auto Prefixer](https://css-tricks.com/autoprefixer/)
* Only global styles belong in app.scss
* All colors belong in _app.variables.scss. If you need to add a new color, add a hex (not rgb) color in this file.

#### Sizing
* Use "ems" for font-sizing
* Use "rems" for margin and padding
* Only use pixels for image sizing
* [Here](https://j.eremy.net/confused-about-rem-and-em/) is a great description on differences.

[CSS Tricks](https://css-tricks.com/) is a great reference for CSS rule-of-thumbs.

# JavaScript
* No trailing whitespace.
* Keep lines fewer than 120 characters.
* Always include a space before and after function parameter.
* Cache jQuery selectors

```
// bad
function setSidebar() {
  $('.sidebar').hide();

  // ...stuff...

  $('.sidebar').css({
    'background-color': 'pink'
  });
}

// good
function setSidebar() {
  var $sidebar = $('.sidebar');
  $sidebar.hide();

  // ...stuff...

  $sidebar.css({
    'background-color': 'pink'
  });
}
```

* If you need to create a JavaScript variable with a value from Django:
```
//string
script(type='text/javascript').
  var stringVar = "{{ string_var }}";
  $(window).on("load", function () {
    if ($("#foo").is(":checked")) {
      $("#bar").val(stringVar);
    }
  });

//json
script(type='text/javascript').
  var jsonVariable = parseJSON("{{ json_variable }}");
  $(window).on("load", function () {
    if ($("#foo").is(":checked")) {
      $("#bar").val(jsonVariable);
    }
  });

//integer
script(type='text/javascript').
  var number = parseInt("{{ number_int }}");
  $(window).on("load", function () {
    if ($("#foo").is(":checked")) {
      $("#bar").val(number);
    }
  });
```
> Putting the `{{ variable }}` inside of quotes will cause linters, PyCharm, Atom, etc. to understand it as JavaScript, and with using things like `parseInt()` or `parseFloat()`, we get the added benefit of being certain what data type the variable will hold!