# YAD Button Processing

## Kinds of YAD Buttons

There are two kinds of YAD buttons, **dialog buttons** and **form buttons**.  They are created
differently and are hosted in different parts of a YAD dialog.

### Dialog Buttons

- Arrayed along the bottom of the dialog
- Included by default (can be added to, changed, or omitted)
- Terminate the dialog (can call command instead)
- Signal the calling function through the exit value

### Form Buttons

- Positioned vertically in line with other form elements
- Must be explicitly included
- Always call another function
- Never terminate the YAD dialog

For questions not answered in this guide, refer also to
[The Buttons of YAD](http://www.thelinuxrain.com/articles/the-buttons-of-yad)
and [this page](http://smokey01.com/yad/).  They may contain information not
covered in this guide.

### Script *yadbuttons*

Pressing the button labelled *YAD Button Processing* on the main dialog will open a new
YAD dialog with buttons labelled according to the sections below.  The *yadbuttons* script
contains all the functions and YAD commands mentioned in this guide.  The dialogs can be
run by clicking the buttons, or studied by opening the script file in a browser (from
github) or a text editor.

### Simple Dialog: function simple_dialog()
The simplest YAD dialog with no options, will include two default buttons.

~~~sh
yad
~~~

![simple_dialog](simple_dialog.png)

The two buttons, Cancel and OK, are automatically added to the bottom of a YAD dialog.

In most cases, pressing a dialog button will terminate or close the dialog.  The identity of the
pressed button is indicated by the yad command's exit value:

~~~sh
yad
exval=$?
case $exval in
   1) echo "You pressed Cancel";;
   0) echo "You pressed OK";;
   252) echo "You pressed ESCAPE key or closed the window.";;
esac
~~~

### Custom Dialog Buttons: function custom_dialog_buttons()

This function and associated invocation of YAD, demonstrates several dialog button
options.  It also introduces a few dialog options and the practice of using an array
to organize the YAD options.

![custom_dialog_buttons](custom_dialog_buttons.png)

~~~sh
# Collect the YAD options
cmd=(
    yad --center --borders=20
    --title="YAD Custom Dialog Buttons"
    --button="Browser":"firefox"
    --button="Announce":"bash -c announce"
    --button="Exit"
)

export -f announce
"${cmd[@]}"
unset announce
~~~

#### Command Array

YAD commands start to get very long as more and more options are used to define a dialog.
Using an array to collect the options makes it easier to read and edit long and complicated
YAD commands.

A BASH array is created between ( ) parentheses, with white space separating array elements.
The spaces between options `yad`,  `--center`, and `--borders=20` create separate array elements
in the same way as does the newline characters between `--borders=20` and
`--title="YAD Custom Dialog Buttons"`.

If an array is used to collect the YAD options, a special BASH variable expansion must be used
to expand the array.  The `"${cmd[@]}"` is enclosed with parentheses to preserve spaces, and
the `[@]` processes the variable as an array, returning each of the array elements separately
to the BASH interpreter and command line options.

#### Dialog Enhancement Options

The `--title` fills the caption bar of the dialog window, and the `--center`, and `--borders`
options improve the dialog's readability by centering the dialog on the screen and including
a border space between the dialog's contents and the borders of the dialog.

#### Custom Buttons

Including any `--button` options will remove the default buttons.  (See also the `announce`
function in the *yadbuttons* script.)  Looking at the buttons in order:

- **Browser** button attempts to open an instance of the *firefox* browser.  The dialog remains
  open.
- **Announce** button calls a local function called `announce`, leaving the dialog open.  Notice
  the `export -f announce`  command that preceeds the YAD invocation `"${cmd[@]}"`.  YAD cannot
  access an internal script function unless it has been exported.  It is a good practice to
  `unset` the function name when it is no longer needed.  Otherwise, the function will remain
  available even after the script has terminated.
- **Exit** button is a standard dialog button that terminates the dialog.  In this case, it is
  not necessary to assign an exit value because, being the only button that terminates the
  dialog, it is not necessary to distinguish between it and any other buttons.

### Too Many Dialog Buttons: function too_many_dialog_buttons

One of the drawbacks of dialog buttons is that they get unwieldly when the buttons are too
numerous or too long.  Consider the following dialog:

![too_many_dialog_buttons](too_many_dialog_buttons.png)
