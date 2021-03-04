# LineEditor

LineEditor is an interative line editor for .NET applications that provides
editing capabilities for an input line with common editing capabilities and
navigation expected in modern application as well as history, incremental
search over the history, completion (both textual or visual) and various 
Emacs-like commands.

The code was originally developed for Mono's interactive C# shell:

![Screenshot of csharp with a completion window](https://github.com/mono/LineEditor/blob/main/LineEditor/csharp.png)

When you create your line editor, you can pass the name of your application, 
which will be used to load and save the history of commands entered by the user
for this particular application.

# Installation

Use NuGet or Paket to install the `Mono.Terminal` NuGet package.

# Example

The following example shows how you can instantiate a line editor that
can provide code completion for some words when the user presses TAB
and how the user can edit them. 

```csharp
using Mono.Terminal;
using System;

class Demo {
    static void Main ()
    {
        // Creates a line editor, and sets the name of the editing session to
        // "foo".  This is used to save the history of input entered
        LineEditor le = new LineEditor ("foo") {
            HeuristicsMode = "csharp"
        };

        // Configures auto-completion, in this example, the result
        // is always to offer the numbers as completions
        le.AutoCompleteEvent += delegate (string text, int pos){
            string prefix = "";
            var completions = new string [] { 
                "One", "Two", "Three", "Four", "Five", 
                "Six", "Seven", "Eight", "Nine", "Ten" };
            return new Mono.Terminal.LineEditor.Completion (prefix, completions);
        };

        string s;

        // Prompts the user for input
        while ((s = le.Edit ("shell> ", "")) != null){
            Console.WriteLine ("Your Input: [{0}]", s);
        }
    }
}
```

# Editing

Users can use the cursor keys to navigate both the text on the current
line, or move back and forward through the history of commands that have
been entered. 

The interactive commands and keybindings are inspired by the GNU bash and
GNU readline capabilities and follow the same concepts found there.

## Kill Buffer: Copy and Paste
Copy and pasting works like bash, deleted words or regions are added to 
the kill buffer. Repeated invocations of the same deleting operation will
append to the kill buffer (for example, repeatedly deleting words) and to
paste the results you would use the Control-y command (yank).

## History and searching

The history search capability is triggered when you press 
Control-r to start a reverse interactive-search
and start typing the text you are looking for, the edited line will
be updated with matches.Typing control-r again will go to the next
match in history and so on.

## Shortcuts

| Shortcut                 | Action performed                                |
| ------------------------ | ----------------------------------------------- |
|Left cursor, `Control`-`b`|Moves the editing point left                     |
|`Alt`-`b`                 |Moves one word back.                             |
|`Alt`-`f`                 |Moves one word forward.                          |
|`↑`, `Control`-`p`        |Selects the previous item in the editing history.|
|`↓`, `Control`-`n`        |Selects the next item in the editing history.    |
|`Home` key, `Control`-`a` |Moves the cursor to the beginning of the line.   |
|`End` key, `Control`-`e`  |Moves the cursor to the end of the line.         |
|`Delete`, `Control`-`d`   |Deletes the character in front of the cursor.    |
|`Backspace`               |Deletes the character behind the cursor.         |
|`Tab`                     |Triggers the completion and invokes the AutoCompleteEvent which gets both the line contents and the position where the cursor is.|
|`Control`-`k`             |Deletes the text until the end of the line and replaces the kill buffer with the deleted text. You can paste this text in a different place by using Control-y.|
|`Control`-`l`             |Clears the screen and forces a refresh of the line editor, useful when a background process writes to the console and garbles the contents of the screen.|
|`Control`-`r`             |Initiates the reverse search in history.         |
|`Alt`-`backspace`         |Deletes the word behind the cursor and adds it to the kill ring. You can paste the contents of the kill ring with Control-y.|
|`Alt`-`d`                 |Deletes the word above the cursor and adds it to the kill ring.  You can paste the contents of the kill ring with Control-y.|
|`Control`-`q`             |Quotes the next input character, to prevent the normal processing of keyhandling to take place|
|`Control`-`c`             |Interrupts editing                               |
