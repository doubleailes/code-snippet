# Maya Code Snippet

## Iterate over top nodes of the current scene

```python
import maya.cmds as mc

# to avoid to get default cameras, we create a set with default nodes
defaultNodes = set(mc.ls(defaultNodes=True))

def top_nodes():

    # iter over every top transform nodes
    for node in mc.ls(assemblies = True):
        
        # skip default nodes and nodes having parent
        if node in defaultNodes:
            continue

        yield node
```

From an empty scene, create a group (ctrl+g):

![Maya empty group](img/maya/maya_null_grp.png)

```python
print list(top_nodes())
# [u'null1']
```

## Iterate over nodes having non-ascii characters in their names

This snippet run though every node in the current scene and detect if there is non ascii characters in node names.

```python
import maya.cmds as mc

def non_ascii_named_nodes():

	# iterate over every node of the current scene
	for node in mc.ls('*'):

		# try to read node name in ascii...
		try:
			node.decode('ascii')
		except UnicodeEncodeError:
			# ...and return the node if its fail
			yield node
```

Create a null node and rename it "pâté" then run the command:

```python
print list(non_ascii_named_nodes())
# [u'p\xe2t\xe9']
```

Maya can print correct values like this:

```python
for node in non_ascii_named_nodes():
    print node
# pâté
```

## Iterate over Maya ui as PySide objects

```python
import maya.OpenMayaUI as omui
from PySide import QtCore, QtGui
import shiboken
 
mayaMainWindow = shiboken.wrapInstance(long(omui.MQtUtil.mainWindow()), QtGui.QWidget)
 
def print_children(widget, depth = 0):
    for child in widget.children():
        print '    '*depth, type(child), child.objectName()
        print_children(child, depth + 1)
 
print_children(mayaMainWindow)
```

More infos about MQtUtil [here](http://help.autodesk.com/view/MAYAUL/2017/ENU/?guid=__cpp_ref_class_m_qt_util_html)

## Add a new menu

```python
import maya.mel as mel

# get main window menu
mainMayaWindow = mel.eval('$nothing = $gMainWindow')

# top menu
menu = mc.menu('Coucou!', parent = mainMayaWindow)

mc.menuItem(label = "Another Manager", command = "print 'another manager'", parent = menu)
```
