#Programming `Flowables`

The following flowables let you conditionally evaluate and execute expressions and statements at wrap time:

##`DocAssign(self, var, expr, life='forever')`

Assigns a variable of name `var` to the expression `expr`. E.g.:

	DocAssign('i',3)


##`DocExec(self, stmt, lifetime='forever')`

Executes the statement `stmt`. E.g.:

	DocExec('i-=1')


##`DocPara(self, expr, format=None, style=None, klass=None, escape=True)`

Creates a paragraph with the value of expr as text.
If format is specified it should use %(__expr__)s for string interpolation
of the expression expr (if any). It may also use %(name)s interpolations
for other variables in the namespace. E.g.:

	DocPara('i',format='The value of i is %(__expr__)d',style=normal)


##`DocAssert(self, cond, format=None)`

Raises an `AssertionError` containing the `format` string if `cond` evaluates as `False`.

	DocAssert(val, 'val is False')


##`DocIf(self, cond, thenBlock, elseBlock=[])`

If `cond` evaluates as `True`, this flowable is replaced by the `thenBlock` elsethe `elseBlock`.

	DocIf('i>3',Paragraph('The value of i is larger than 3',normal), Paragraph('The value of i is not larger than 3',normal))


##`DocWhile(self, cond, whileBlock)`

Runs the `whileBlock` while `cond` evaluates to `True`. E.g.:

	DocAssign('i',5)
	DocWhile('i',[DocPara('i',format='The value of i is %(__expr__)d',style=normal),DocExec('i-=1')])


This example produces a set of paragraphs of the form:

	The value of i is 5
	The value of i is 4
	The value of i is 3
	The value of i is 2
	The value of i is 1
