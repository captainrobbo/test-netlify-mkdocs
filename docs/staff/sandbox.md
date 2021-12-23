# Experiment here


## Add basic code snippet
In simple cases, you do not need to add any special `<code>` tags or markers, just indent in the md file and mkdocs automatically adds `<code>` at build time; 

    from reportlab.pdfgen import canvas
    def hello(c):
        c.drawString(100,100,"Hello World")
    c = canvas.Canvas("hello.pdf")
    hello(c)
    c.showPage()
    c.save()


##How to Tab

Useful extentions info pymdownx https://facelessuser.github.io/pymdown-extensions/extensions/arithmatex

## Tabs
=== "Tab1"
		```
		Content
		Code
		Texts
		```
=== "Tab2"
		```
		image 1
		texts
		content
		```
=== "Tab3"
		```
		image 1
		texts
		content
		```

##Add an image
![Image]({{static_root}}rmlfornewbies/png_examples/example1.png)<br/>

## Double Curly brackets

{% raw %}Hello, my name is {{name}}.{% endraw %}

{% raw %}
```
I'm a code block that contains {{handlebars}}
with highlighting.
```
{% endraw %}

