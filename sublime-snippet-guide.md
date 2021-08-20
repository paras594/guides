# Sublime Snippets Guide



### TL;DR

```
sublime > tools > developers > new snippets
```

- `$1` Tab stop
- `${1:placholder}`  Tab stop with placeholder
- `${1/\*//}`  Multiple edits without multiple cursors

```
// save snippet here
sublime > preferences > browse packages > User > yourSnippet.sublime-snippet
```



### Creating Snippets

```
sublime > tools > developers > new snippets
```



### Default Snippet

WARNING: do not remove the `<![CDATA[...]]` . Write snippet between the square brackets only.

```xml
<snippet>
	<content><![CDATA[
Hello, ${1:this} is a ${2:snippet}.
]]></content>
	<!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
	<!-- <tabTrigger>hello</tabTrigger> -->
	<!-- Optional: Set a scope to limit where the snippet will trigger -->
	<!-- <scope>source.python</scope> -->
</snippet>
```



### Basic Snippet

```xml
<snippet>
	<content><![CDATA[
hello world
]]></content>
	<!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
	<tabTrigger>hello</tabTrigger>
	<!-- Optional: Set a scope to limit where the snippet will trigger -->
	<scope>source.python, text.html</scope>
</snippet>
```



- content tags handle the code snippet
- tab trigger tags create trigger for our snippet. When that trigger word appears, we will be able to press `tab` and use the snippet
- scope tags limit the usage and trigger of snippets to a specific type of file.
- we can also add multiple scopes by comma separating them

Reference for scope: [Link 1](https://gist.github.com/iambibhas/4705378), [Link2](https://gist.github.com/J2TeaM/a54bafb082f90c0f20c9)



### Snippet With Tab Stops

```xml
<snippet>
	<content><![CDATA[
<div class="$1">
	hello, $2
</div> <!-- /.$1 -->
]]></content>
	<!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
	<tabTrigger>hello</tabTrigger>
	<!-- Optional: Set a scope to limit where the snippet will trigger -->
	<scope>text.html</scope>
</snippet>
```



- `$1` are tab stops. When added at multiple places, creates multiple cursors



### Snippets with Tab Stops and Placeholder text

We can also add a tab stop with placeholder text at that tab stop

```xml
<snippet>
	<content><![CDATA[
<div class="$1">
	hello, ${2:text here}
</div> <!-- /.$1 -->
]]></content>
	<!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
	<tabTrigger>hello</tabTrigger>
	<!-- Optional: Set a scope to limit where the snippet will trigger -->
	<scope>text.html</scope>
</snippet>

```



- `${2:text here}` this syntax is used when we want to add a placeholder for the tab stop



### Snippet without multiple cursors

To avoid multiple cursors at different tab stops of same number or type, use the following syntax:

```xml
<snippet>
	<content><![CDATA[
<div class="$1">
	hello, ${2:text here}
</div> <!-- /.${1/\*//} -->
]]></content>
	<!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
	<tabTrigger>hello</tabTrigger>
	<!-- Optional: Set a scope to limit where the snippet will trigger -->
	<scope>text.html</scope>
</snippet>

```



- `${1/\*//}` It is a kind of regex that helps us achieve our goal to edit different lines without multiple cursor



### Capitalize the first word in multiple cursors

```xml
<snippet>
  <content><![CDATA[
const [${1:state}, set${1/(.+)/\u$1/g}] = useState($2);
]]></content>
  <tabTrigger>snusestate</tabTrigger>
  <description>useState</description>
  <!-- Optional: Set a scope to limit where the snippet will trigger -->
  <scope>source.js meta.function.js.fjsx15 meta.block.js.fjsx15</scope>
</snippet>
```



- `${1/(.+)/\u$1/g}` Capitalizes the first word



### Saving snippets

Snippets are saved in the following directory

```
sublime > preferences > browse packages > User > yourSnippet.sublime-snippet
```



### Access current filename in snippet

$TM_FILENAME gives you the filename with extension, command below will give you filename without extension

```
${TM_FILENAME/(\w+)\.js/\1/g}
```



### Environment Variables in snippets

Reference: [Link](https://docs.sublimetext.io/guide/extensibility/snippets.html#environment-variables)