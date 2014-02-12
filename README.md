# COMPER Template Parser


## Docs

### parse( $source, $data, $config  )

#### $source

##### Parsing files

Put filename without extension as first parameter.

```php
$this->parser->parse('template_file');
```

##### Parsing strings
```php
$this->parser->parse('Hello!', array(), array('is_string' => TRUE));
```


#### $data

##### Pseudo-variables

Defined as **simple array**.

```php
$data = array
(
	'pseudo-variable' => 'Some data',
	'content' => 'Some new exciting content',
	'author' => 'That is me!'
);
 
$this->parser->parse('template_file', $data);
```

Shown using {} brackets.
```html
<h1>{pseudo-variable}</h1>
<p>{content}</p>
<small>Author: {author}</small>
```

**Multi-dimensional** array:
```php
$data = array
(
	'pseudo-variable' => 'Some data',
	'content' => 'Some new exciting content',
	'author' => array( 'name' => 'Tomas', 'email' => 'tomas@home.com' )
);
```

Can be displayed using arrow ( -> ):
```html
<h1>{pseudo-variable}</h1>
<p>{content}</p>
<small>Author: {author->name} ({author->email})</small>
```

You can go to unlimited deep.

**Modificators** can be used as much as you need, with parameters:

```html
{name|trim|ucfirst|str_replace[&, " ", "-"]}
```

Variables inside variables can be also used.

```html
{lang->{lang}->error_msg}
```

Ampersand (&) stands for 'this'.

##### Cycles

```php
$data = array('user' => array
(
	array('username' => 'life', 'email' => 'life@earth.zz', 'address' => 'Earth 001, Milky way'),
	array('username' => 'anonym', 'email' => 'seeyou@friday.zz', 'address' => 'Paris, France')
));
```

In TPL's defined using syntax:
```html
<!-- BEGIN {user} -->
<div class="user">
	<div> Username: {username} </div>
	<div> Email: {email} </div>
	<div> Address: {address} </div>
</div>
<!-- END {user} -->
```

Naming cycle as recommended. You can also use prefixes and nested cycles. Notice BEGIN and END formula for naming cycle and second line for prefix.

```html
<!-- BEGIN {user} AS user -->

	Name: {user.name}
	
	<!-- BEGIN {user.friends} AS friend -->
		Name: {friend.name}
	<!-- END friends -->
	
<!-- END user -->
```

There is special pseudo-variable generated by cycles {__i} as iterator of current loop.

```html
<!-- BEGIN {user} AS user -->

	<!-- IF {__i} == 0 -->
	
		<div class="main">
		
			<img src="{image}">
			<p> {content }</p>
		
		</div>
	
	<!-- ELSE -->
	
		<div class="featured"> {content} </div>
	
	<!-- END -->
	
<!-- END user -->
```	

##### Conditions

Are very intuitive. You can use php functions and also modificators (recommended).

```html
<!-- IF {users|count} > 0 -->

	<!-- BEGIN users -->
	...
	<!-- END users -->
	
<!-- ELSE -->
	...
<!-- END -->
```

```html
<!-- IF {day} == 1 -->
Monday
<!-- ELSEIF {day} == 2 -->
Thuesday
<!-- ELSEIF {day} == 3 -->
...
<!-- END -->
```

```html
<!-- IF is_numeric( {pow} ) && pow( {num}, 2 ) > 4 -->
	...
<!-- ELSE -->
	...
<!-- END -->
```


#### $config

option             | default value                         | meaning
-------------------|---------------------------------------|----------------------------------------------------|
allow_modificators | TRUE                                  | Allow modificators                                 |
append             | array()                               | Set 'common' arrays for append() function          |
clean              | TRUE                                  | Delete pseudovalues                                |
disable_appends    | FALSE                                 | Turns appends off                                  |
disable_conditions | FALSE                                 | Turns conditions off                               |
disable_cycles	   | FALSE                                 | Turns cycles off                                   |
disable_includes   | FALSE                                 | Turns includes off                                 |
disable_variables  | FALSE                                 | Turns variables off                                |
exceptions         | array('memory_usage', 'elapsed_time') | Ignored pseudo-variables                           |
extension          | 'tpl'                                 | Extension of templates                             |
is_string          | FALSE                                 | Parse from string (when false, it's parsing files) |
path               | '%path%/views/'                       | Location of templates                              |
show               | TRUE                                  | Show the result                                    |
suffix_theme_only  | TRUE                                  | Use template_suffix only when themes are used      |
template_suffix    | 'tpl'                                 | Directory of templates in view folder              |
theme              | ''                                    | Set theme for your project                         |


### theme( $theme_name  )
Your folder structure might look like (if you want to use themes):

```
/views/
	/my_theme 
		/css 
			style.css 
		/img 
		/js 
		/tpl (this folder is required) 
			template_file.tpl

	/my_second_theme 
		/css 
			style.css 
		/img 
		/js 
		/tpl (this folder is required) 
			template_file.tpl
```

You can simply switch between themes:
```php
$this->parser->theme( 'my_second_theme' );
```

### append( $name, $array )

Passing data into parser outside of parser() function. Can be used in models that are autoloaded.

```php
$this->parser->append( 'urls', array( 'apple' => array( 'url' => 'http://apple.com', 'title' => 'Apple Homepage') ) );
$this->parser->parse( 'template' );
```	

```html
<a href="{urls->apple->url}"> {urls->apple->title} </a>
```

### Template only syntax

#### Includes

You can load one template into another one. Data are global, so can be used in both templates. You are writting filename without extension.

```html
<!-- INCLUDE header -->
```

#### Nocode

To specify area of template that will NOT be parsed use (very useful when using Angular.js):

```html

	Value: {item}
	Syntax: <!-- NOCODE --> {item} <!-- /NOCODE -->
```
