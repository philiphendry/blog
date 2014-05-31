---
author: philiphendry
comments: true
date: 2008-07-15 19:04:57+00:00
layout: page
slug: exam-70-526-microsoft-net-framework-20-windows-based-client-development
title: 'Exam 70-526 : Microsoft .Net Framework 2.0 Windows-Based Client Development'
wordpress_id: 83
---

 

These are notes taken by myself whilst reading the Microsoft Press Self-Paced Training Kit book.

 

## Chapter 1: Windows Forms and the User Interface

 

### Lesson 1 : Adding and Configuring Windows Forms

 

Form properties :

 

  
  * Name (only set at design time) 
   
  * BackColor 
   
  * BackgroundImage 
   
  * BackgroundImageLayout 
   
  * ControlBox (determines whether the form has a control/system menu box) 
   
  * Cursor 
   
  * Enabled 
   
  * Font 
   
  * ForeColor (a _Color_ used to display text and which will be adopted by all contained controls unless their _ForeColor_ is set) 
   
  * FormBorderStyle (None, FixedSingle, Fixed3D, FixedDialog, Sizable, FixedToolWindow, SizableToolWindow) 
   
  * HelpButton 
   
  * Icon 
   
  * Location 
   
  * MaximizeBox 
   
  * MaximumSize 
   
  * MinimizeSize 
   
  * MinimumSize 
   
  * Opacity (% where 0.0 is completely transparent, 1.0 opaque) 
   
  * Size ( = new _Size_(300, 200) or .Width = 300& .Height = 200) 
   
  * StartPosition (_Manual_ (determined by Location property), _CenterScreen, WindowsDefaultLocation_ (size determined by Size property), _WindowsDefaultBounds_ (size determined by Windows default), _CenterParent_) 
   
  * Text (the caption of the form) 
   
  * TopMost (_boolean_) 
   
  * Visible 
   
  * WindowState (_Normal, Minimized, Maximized_) 
 

#### Setting the startup form

 

Startup object is specified in Main in Program.cs :

 
    
    [<span style="color:#2b91af;">STAThread</span>]
    <span style="color:blue;">static void </span>Main()
    {
        <span style="color:#2b91af;">Application</span>.EnableVisualStyles();
        <span style="color:#2b91af;">Application</span>.SetCompatibleTextRenderingDefault(<span style="color:blue;">false</span>);
        <span style="color:#2b91af;">Application</span>.Run(<span style="color:blue;">new </span><span style="color:#2b91af;">Form1</span>());
    }


[](http://11011.net/software/vspaste)



#### Non-Rectangular Windows Forms




    
    <span style="color:blue;">private void </span>Form1_Load(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
    {
        System.Drawing.Drawing2D.<span style="color:#2b91af;">GraphicsPath </span>path = <span style="color:blue;">new </span>System.Drawing.Drawing2D.<span style="color:#2b91af;">GraphicsPath</span>();
        path.AddEllipse(0, 0, <span style="color:blue;">this</span>.Width, <span style="color:blue;">this</span>.Height);
        <span style="color:blue;">this</span>.FormBorderStyle = <span style="color:#2b91af;">FormBorderStyle</span>.None;
        <span style="color:blue;">this</span>.Region = <span style="color:blue;">new </span><span style="color:#2b91af;">Region</span>(path);
    }


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)







### Lesson 2 : Managing Control Layout with Container Controls





Example container controls : _Panel, FlowLayoutPanel_ and_ SplitContainer. _Changes to properties on the container may also affect all contained controls - for example, setting _Enabled_ on the container disables all contained controls regardless of the individual properties.





#### Controls Collection





Every form and control has a _Controls_ collection. New controls can be added programmatically by creating the control and adding to the collection :




    
    <span style="color:#2b91af;">Button </span>button = <span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>();
    button.Text = <span style="color:#a31515;">"Press me!"</span>;
    button.Location = <span style="color:blue;">new </span><span style="color:#2b91af;">Point</span>(20, 20);
    <span style="color:blue;">this</span>.Controls.Add(button);


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)







#### Anchor and Dock Properties





The _Anchor_ property can be set to any combination of _Top, Bottom, Left, Right_ or none of these. These will cause different sides of the control to ‘anchor’ to a particular side of it’s containing parent and if opposite sides are specified the control will stretch. For example the following button will stretch horizontally when the form is resized :




    
    button.Anchor = <span style="color:#2b91af;">AnchorStyles</span>.Left | <span style="color:#2b91af;">AnchorStyles</span>.Right;

















The _Dock_ property can be set to one of _Left, Right, Top, Bottom, Fill _or _None_ and ‘docks’ the control to the parent container :




    
    button.Dock = <span style="color:#2b91af;">DockStyle</span>.Top;





[](http://11011.net/software/vspaste)





























#### The GroupBox Container





A _GroupBox_ is commonly used for grouping _RadioButton_ controls :





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb1.png)](http://philiphendry.files.wordpress.com/2008/07/image1.png)





The caption can be set using the _Text_ property or removed completely by setting to empty string.





#### The Panel Container





A _Panel_s _BorderStyle_ property can be set to _None, FixedSingle_ or _Fixed3D_. The _Panel_ supports both horizontal and vertical scrollbars which will be automatically available if _AutoScroll_ is _True_ and a child control is outside the visible region.





#### The FlowLayoutPanel Container





Inherits from _Panel_ but automatically repositions controls when it’s resized. _FlowDirection_ can be set to _LeftToRight, RightToLeft, ToDown _and_ BottomUp_ and determines how controls are drawn and wrapped if _WrapContents_ is _True_. Calling _SetFlowBreak(Control, bool)_ and passing a child control will force the container to wrap before continuing drawing controls. _bool GetFlowBreak(Control)_ can be_ _called to discover if a flow break has been set.









#### The TableLayoutPanel Container





Inherits from _Panel_ :




    
    <span style="color:#2b91af;">TableLayoutPanel </span>pnl = <span style="color:blue;">new </span><span style="color:#2b91af;">TableLayoutPanel</span>();
    <span style="color:blue;">this</span>.Controls.Add(pnl);
    
    pnl.AutoScroll = <span style="color:blue;">true</span>;
    pnl.CellBorderStyle = <span style="color:#2b91af;">TableLayoutPanelCellBorderStyle</span>.Single;
    pnl.ColumnStyles.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">ColumnStyle</span>());
    pnl.ColumnStyles.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">ColumnStyle</span>());
    pnl.RowStyles.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">RowStyle</span>());
    pnl.RowStyles.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">RowStyle</span>());
    <span style="color:#2b91af;">Debug</span>.WriteLine(pnl.ColumnCount);
    <span style="color:#2b91af;">Debug</span>.WriteLine(pnl.RowCount);
    pnl.ColumnStyles[0].SizeType = <span style="color:#2b91af;">SizeType</span>.Percent;
    pnl.ColumnStyles[0].Width = 50;
    pnl.ColumnStyles[1].SizeType = <span style="color:#2b91af;">SizeType</span>.Percent;
    pnl.ColumnStyles[1].Width = 50;
    pnl.RowStyles[0].SizeType = <span style="color:#2b91af;">SizeType</span>.Absolute;
    pnl.RowStyles[0].Height = 50;
    pnl.RowStyles[1].SizeType = <span style="color:#2b91af;">SizeType</span>.Absolute;
    pnl.RowStyles[1].Height = 50;
    pnl.Controls.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>(), 0, 0);
    pnl.Controls.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>(), 0, 1);
    pnl.Controls.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>(), 1, 0);
    pnl.Controls.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>(), 1, 1);
    
    pnl.GrowStyle = <span style="color:#2b91af;">TableLayoutPanelGrowStyle</span>.AddRows;
    pnl.Controls.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>());


[](http://11011.net/software/vspaste)



#### The TabControl




    
    <span style="color:#2b91af;">TabControl </span>tab = <span style="color:blue;">new </span><span style="color:#2b91af;">TabControl</span>();
    <span style="color:blue;">this</span>.Controls.Add(tab);
    
    tab.TabPages.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">TabPage</span>());
    tab.TabPages.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">TabPage</span>());
    tab.TabPages[0].Text = <span style="color:#a31515;">"First"</span>;
    tab.TabPages[0].AutoScroll = <span style="color:blue;">true</span>;
    tab.TabPages[0].BorderStyle = <span style="color:#2b91af;">BorderStyle</span>.Fixed3D; <span style="color:green;">// or FixedSingle, None
    
    </span>tab.Appearance = <span style="color:#2b91af;">TabAppearance</span>.Normal; <span style="color:green;">// or Buttons, FlatButtons
    </span>tab.Alignment = <span style="color:#2b91af;">TabAlignment</span>.Left; <span style="color:green;">// or Top, Bottom, Right
    </span>tab.Multiline = <span style="color:blue;">true</span>;


[](http://11011.net/software/vspaste)







#### The SplitContainer




    
    <span style="color:#2b91af;">SplitContainer </span>split = <span style="color:blue;">new </span><span style="color:#2b91af;">SplitContainer</span>();
    <span style="color:blue;">this</span>.Controls.Add(split);
    
    split.Dock = <span style="color:#2b91af;">DockStyle</span>.Fill;
    split.Orientation = <span style="color:#2b91af;">Orientation</span>.Vertical; <span style="color:green;">// or Horizontal
    </span>split.IsSplitterFixed = <span style="color:blue;">false</span>; <span style="color:green;">// if true, use SplitterDistance
    </span>split.SplitterDistance = 200; <span style="color:green;">// in pixels
    </span>split.SplitterWidth = 30;
    split.Panel1MinSize = 40;
    split.Panel2MinSize = 20;
    
    <span style="color:green;">// The following are mutually exclusive!!
    </span>split.Panel1Collapsed = <span style="color:blue;">false</span>;
    split.Panel2Collapsed = <span style="color:blue;">false</span>;
    
    <span style="color:green;">// FixedPanel decides how to resize panels when the
    // SplitContainer is resized
    </span>split.FixedPanel = <span style="color:#2b91af;">FixedPanel</span>.Panel1; <span style="color:green;">// or None, Panel2
    
    // Set the border style of the two SplitterPanels
    </span>split.BorderStyle = <span style="color:#2b91af;">BorderStyle</span>.FixedSingle;
    
    <span style="color:green;">// There are two SplitterPanel controls either side of the split
    </span>split.Panel1.Controls.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>());
    split.Panel2.Controls.Add(<span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>());


[](http://11011.net/software/vspaste)



## Chapter 2 : Configuring Controls and Creating the User Interface





### Lesson 1 : Configuring Controls in Windows Forms





#### Control Overview





Inherited properties from _Control_ :






  
  * Anchor 


  
  * BackColor 


  
  * BackgroundImage 


  
  * CausesValidation 


  
  * ContainsFocus (or one of it’s children has focus!) 


  
  * Controls (used only for containers) 


  
  * Cursor 


  
  * Dock 


  
  * Enabled 


  
  * Font 


  
  * ForeColor 


  
  * HasChildren 


  
  * Height 


  
  * Location 


  
  * MaximumSize 


  
  * MinimumSize 


  
  * Name 


  
  * Parent (setting this assigns the control to the parents collection!) 


  
  * Region (gets or sets the window region associated with the control) 


  
  * Size 


  
  * TabOrder 


  
  * Tag (a value or object) 


  
  * Text 


  
  * Visible 


  
  * Width 





#### Best Practices for User Interface Design














  
  * Simplicity 


  
  * Position of Controls 
    
      
    * Relative importance 


      
    * Frequency of use 

    
  


  
  * Consistency 


  
  * Aesthetics 





#### Summary (things to remember)






  
  * Snaplines 


  
  * Smart tags 


  
  * Document Outline Window 





### Lesson 2 : Creating and Configuring Command and Text Display Controls





#### The Button Control





Important properties :






  
  * AutoEllipsis (handling of text that extends beyond the width) 


  
  * DialogResult (can associate a _DialogResult_ value such as _OK_ or _Cancel_) 

    
      
    * Use _ShowDialog()_ to retrieve the DialogResult : 

    
  




    
    <span style="color:#2b91af;">DialogResult </span>result = <span style="color:blue;">this</span>.ShowDialog();


[](http://11011.net/software/vspaste)




  
  * FlatAppearance (determines the style when _FlatStyle = Flat_) 

    
      
    * an instance of _FlatButtonAppearanc_ with members : 

        
          
      * BorderColor 


          
      * BorderSize 


          
      * MouseDownBackColor 


          
      * MouseOverBackColor 

        
      

    
  


  
  * FlatStyle (Flat, Popup, Standard, System - the style when a user moves mouse over and clicks – only the left mouse button will give this visual feedback) 


  
  * Text 


  
  * TextAlign 





Events :






  
  * Button.Click 


  
  * Button.MouseDown 
    
      
    * MouseClickEventArgs : 
        
          
      * Button (Left, Right, Middle, None, XButton1, XButton2) 


          
      * Clicks (a count) 


          
      * Delta (‘notch’ count for mouse wheel rotation) 


          
      * Location 


          
      * X 


          
      * Y 

        
      

    
  

























#### The Label Control





Important properties :






  
  * AutoSize (boolean determining whether the control automatically resizes to the contents of _Text_) 


  
  * UseMnemonic (boolean determining whether the label specifies an access key) 


  
  * TabIndex (must be set to determine which control is associated to the access key) 


  
  * Text 
    
      
    * Precede an access key with &

    
  





#### The LinkLabel Control





Important properties :






  
  * ActiveLinkColor 


  
  * LinkArea 
    
      
    * an instance of _LinkArea_ with members : 

        
          
      * Start 


          
      * Length 

        
      

    
  


  
  * LinkBehavior (SystemDefault, AlwaysUnderline, HoverUnderline, NeverUnderline) 


  
  * LinkColor 


  
  * LinkVisited 


  
  * Links 
    
      
    * a collection of _LinkLabel.Link_ objects with members : 

        
          
      * Start 


          
      * Length 


          
      * LinkData 


          
      * Name 


          
      * Visited 


          
      * Tag 


          
      * Enabled 

        
      

    
  


  
  * VisitedLinkColor 


  
  * Text 





Events :






  
  * LinkLabel.LinkClicked 





### Lesson 3 : Creating and Configuring Text Edit Controls





#### The TextBox Control





Properties :






  
  * AutoCompleteCustomSource (a string of auto-complete data) 


  
  * AutoCompleteMode (None, Append, Suggest, SuggestAppend) 


  
  * AutoCompleteSource : 
    
      
    * When set to _CustomSource_ the _AutoCompleteCustomSource _property is used. 


      
    * [![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb2.png)](http://philiphendry.files.wordpress.com/2008/07/image2.png)

    
  


  
  * CharacterCasting (Normal, Upper, Lower) 


  
  * Lines (a string array containing each individual line) 


  
  * MaxLength 


  
  * MultiLine 


  
  * PasswordChar 


  
  * ReadOnly 


  
  * ScrollBars (None, Both, Horizontal, Vertical) 


  
  * Text 


  
  * UseSystemPasswordChar (overrides the _PasswordChar_) 


  
  * WordWrap (applies when _MultiLine_ is _true _and effectively disables horizontal scroll bars) 





#### The MaskedTextBox Control 





#### [![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb3.png)](http://philiphendry.files.wordpress.com/2008/07/image3.png)





Properties :






  
  * AllowPromptAsInput 


  
  * AsciiOnly (only A-Z and a-z are accepted) 


  
  * BeepOnError 


  
  * CutCopyMaskFormat (an enum from _MaskFormat_ : ExcludePromptAndLiterals, **IncludeLiterals**, IncludePrompt, IncludePromptAndLiterals) 


  
  * HidePromptOnLeave 


  
  * InsertKeyMode 


  
  * Mask 


  
  * PromptChar (default is underscore) 


  
  * RejectInputOnFirstFailure (rejects pasted input if _true_ otherwise accepts characters that match) 


  
  * ResetOnPrompt 


  
  * ResetOnSpace 


  
  * SkipLiterals 


  
  * TextMaskFormat 





Masking options for the _Mask_ property string :






  
  * 0 (required digit) 


  
  * 9 (optional digit) 


  
  * # (optional digit, space, + or –) 


  
  * L (required upper or lower case letter) 


  
  * ? (optional upper or lower case letter) 


  
  * & (required character – behaves like L if _AsciiOnly = true_) 


  
  * C (optional character – behaves like L if _AsciiOnly = true_) 


  
  * A, a (optional alphanumeric – behaves like ? if _AsciiOnly = true_) 


  
  * . (decimal placeholder taken from _FormatProvider_) 


  
  * , (thousands placeholder taken from _FormatProvider_) 


  
  * : (time separator taken from _FormatProvider_) 


  
  * / (Date separator taken from _FormatProvider_) 


  
  * $ (Currency symbol taken from _FormatProvider_) 


  
  * < (convert to lowercase) 


  
  * > (convert to uppercase) 


  
  * | (disables previous < or > ) 


  
  * \ (escape a mask character) 


  
  * all other characters appear in the mask and form part of the output 





















## Chapter 3 : Advanced Windows Forms Controls





### Lesson 1 : Creating and Configuring List-Display Controls





Remember the _IList_ interface, shown below, that is used on the majority of the collections in all the controls :





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb4.png)](http://philiphendry.files.wordpress.com/2008/07/image4.png)









#### The ListBox : ListControl Control





Properties :






  
  * DataSource 


  
  * DisplayMember 


  
  * FormatString (if _FormattingEnabled = true)_


  
  * FormattingEnabled 


  
  * Items (a _ListBox.ObjectCollection_) 


  
  * MultiColumn 


  
  * SelectedIndex 
    
      
    * setting this property multiple times will select multiple items if _SelectionMode = Multi****_

    
  


  
  * SelectedIndices (a _ListBox.SelectedIndexCollection_) 


  
  * SelectedItem (a _ListBox.SelectedItem)_ 

    
      
    * setting this property multiple times will select multiple items if _SelectionMode = Multi****_

    
  


  
  * SelectedItems (a _ListBox.SelectedObjectCollection_) 


  
  * SelectedValue (an _object_) 


  
  * SelectionMode (None, Single, MultiSimple, MultiExtended) 
    
      
    * Extended allows use of _Shift_ and _Ctrl_ keys 

    
  


  
  * Sorted (_boolean_) 


  
  * ValueMember 





Operations :






  
  * Add(object item) 


  
  * AddRange(ListBox.ObjectCollection value) 


  
  * AddRange(object[] items) 





#### The ComboBox : ListControl Control





Properties :






  
  * DataSource 


  
  * DisplayMember 


  
  * DropDownHeight (a maximum) 


  
  * DropDownStyle (Simple – similar to _List_, **DropDown**, DropDownList – which doesn’t allow user to type a new value) 


  
  * DropDownWidth 


  
  * FormatString (if _FormattingEnabled = true_) 


  
  * FormattingEnabled 


  
  * Items (a _ComboBox.ObjectCollection_) 


  
  * SelectedIndex 


  
  * SelectedItem (an _Object_) 


  
  * SelectedValue (an _Object_) 


  
  * ValueMember 





#### The CheckedListBox : ListBox Control

















Properties :






  
  * CheckedIndices (a _CheckedListBox.CheckedIndexCollection _of _int_) 


  
  * CheckedItems (a _CheckedListBox.CheckedItemCollection_ of_ Object_) 


  
  * CheckOnClick (check the item when selected if _true_) 





Operations :






  
  * SetItemChecked(int index, bool value) 


  
  * SetItemCheckState(int index, CheckState state) (where state is _Checked, Unchecked, Indeterminate_) 





#### FormatString





The _FormatString_ can contain the following format characters :






  
  * 0 (a zero placeholder will be displayed) 


  
  * # (a digit placeholder which drops left-most digits that aren’t supplied) 


  
  * . (a decimal separator determined by locale. Only the first is used) 


  
  * , (thousands separator determined by locale _NumberFormatInfo.NumberGroupSeparator. _Commas immediately preceeding the decimal point will be treated as a divider and therefore 0,, will represent 100,000,000 as just 100) 


  
  * % (percentage placholder causes any number to be multiplied by 100 before displaying and % appears where it appears in the format string) 


  
  * E0, E+0, E-0, e0, e+0, e-0 (scientific notation are always followed by at least one 0) 


  
  * \ (escape character e.g. \n ) 


  
  * “ABC”, ‘ABC’ (literal strings) 


  
  * ; (section separator allow up to three sections one each for positive, negative and zero numbers respectively) 


  
  * other characters (literal strings) 













































#### The ListView : Control Control





Properties :






  
  * Columns (a _ListView.ColumnHeaderCollection_ containing _ColumnHeader_s with members : ) 

    
      
    * DisplayIndex 


      
    * ImageIndex 


      
    * ImageKey 


      
    * ImageList 


      
    * Index 


      
    * Name 


      
    * Tag 


      
    * Text 


      
    * TextAlign (_Left, Right, Center_) 


      
    * Width 

    
  


  
  * Groups (a _ListViewGroupCollection _containing _ListViewGroup_s with members : ) 

    
      
    * Header 


      
    * HeaderAlignment (_Left, Right, Center_) 


      
    * Items (an _ListView.ListViewItemCollection_) 


      
    * Name 


      
    * Tag 

    
  


  
  * Items (a _ListView.ListViewItemCollection_ containing _ListViewItem_s with members :) 

    
      
    * BackColor 


      
    * Bounds 


      
    * Checked 


      
    * Focused 


      
    * Font 


      
    * ForeColor 


      
    * Group (a _ListViewGroup_) 


      
    * ImageIndex (if set then _ImageKey = “”_ ) 


      
    * ImageKey (if set then _ImageIndex_ = _null_) 


      
    * ImageList (an _ImageList_) 


      
    * IndentCount 


      
    * Index 


      
    * Name 


      
    * Position (a _Point_) 


      
    * Selected 


      
    * StateImageIndex 


      
    * SubItems (a _ListViewItem.ListViewSubItemCollection _containing _ListViewSubItem_s, displayed when _View = Details, _correspond to _ListView.Columns _and with members :) 

        
          
      * BackColor 


          
      * Bounds 


          
      * Font 


          
      * ForeColor 


          
      * Name 


          
      * Tag 


          
      * Text 

        
      


      
    * Tag 


      
    * Text 


      
    * ToolTipText 

    
  


  
  * LargeImageList (an _ImageList_ which has a property _Images_ which is a _ImageList.ImageCollection._ Rendered when _View = LargeIcon_) 


  
  * ShowGroups (show the groups in the _Groups_ collection) 


  
  * SmallImageList (an _ImageList. _Rendered when _View = SmallIcon_) 


  
  * View (_LargeIcon, SmallIcon, Details, List, Title_) 





#### The TreeView Control





Properties :






  
  * Checkboxes 


  
  * ShowLines 


  
  * ShowNodeToolTips 


  
  * ShowPlusMinus 


  
  * ShowRootLines 


  
  * Sorted 


  
  * TopNode 


  
  * TreeViewNodeSorter (an _IComparer_) 


  
  * Nodes (a _TreeNodeCollection _containing a collection of _TreeNode _which has properties: ) 

    
      
    * Checked 


      
    * FullPath 


      
    * FirstNode 


      
    * LastNode 


      
    * NextNode 


      
    * NextVisibleNode 


      
    * Nodes 


      
    * Parent (throws _NullReferenceException_ if current node is the root node) 


      
    * PrevNode 


      
    * PrevVisibleNode 


      
    * TreeView (the containing _TreeView_) 


      
    * ImageIndex 


      
    * ImageKey 


      
    * IsEditing 


      
    * IsExpanded 


      
    * IsSelected 


      
    * IsVisisble 


      
    * Level 


      
    * ToolTipText 
          


          
and operations : 


      
    * Collapse 


      
    * EnsureVisible 


      
    * Expand 


      
    * ExpandAll 


      
    * GetNodeCount 


      
    * Toggle 

    
  





#### The NumericUpDown :UpDownBase Control





Properties :






  
  * Accelerations (a _NumericUpDownAccelerationCollection_) 


  
  * DecimalPlaces 


  
  * Hexadecimal (a _bool_) 


  
  * Increment (a _decimal_) 


  
  * Maximum (a _decimal_) 


  
  * Minimum (a _decimal_) 


  
  * ThousandsSeparator (a_ bool_) 


  
  * Value (a _decimal_) 









#### The DomainUpDown Control





Properties :






  
  * Items (a _DomainUpDown.DomainUpDownItemCollection_ which derives from ArrayList and is a collection of _Object. _It contains an array of strings to scroll through in the UpDown control) 


  
  * ReadOnly 


  
  * SelectedIndex 


  
  * SelectedItem (an _Object_) 


  
  * Sorted 


  
  * Text 


  
  * Wrap 





### Lesson 2 : Creating and Configuring Value-Setting, Date-Setting and Image-Display Controls





#### The CheckBox : ButtonBase Control





Properties :






  
  * AutoCheck (a _bool_ indicating whether clicking the text sets the check) 


  
  * Checked 


  
  * CheckState (_Checked, Unchecked, Indeterminate _can initially be set to _Indeterminate_ if _ThreeState = false_ but doesn’t allow it to be re-selected) 


  
  * Text 


  
  * ThreeState (a _bool_) 









#### The RadioButton : ButtonBase Control





All _RadioButton_ controls in an container (commonly a _GroupBox_) are mutually exclusive.





Properties :






  
  * Checked 


  
  * Text 





#### The TrackBar Control





Properties :






  
  * LargeChange (size of slider movement for page up/down) 


  
  * Maximum 


  
  * Minimum 


  
  * SmallChange (size of slider movement for arrow keystrokes) 


  
  * TickFrequency (count of positions between ticks) 


  
  * TickStyle (_None, TopLeft, BottomRight, Both_) 


  
  * Value (an _int_) 





#### The DateTimePicker Control





Properties :






  
  * CustomFormat (when _Format = Custom_) 


  
  * Format (_Long, Short, Time, Custom_) 


  
  * MaxDate 


  
  * MinDate 


  
  * Value 





#### The MonthCalendar Control





Properties :






  
  * AnnuallyBoldedDates (a _DateTime[]_ ) 


  
  * BoldedDates(a _DateTime[]_ ) 


  
  * FirstDayOfWeek (a _Day_ eg. _Monday_) 


  
  * MaxDate 


  
  * MinDate 


  
  * MaxSelectionCount (the maximum numbers of days that can be selected) 


  
  * MonthlyBoldedDates (a _DateTime[]_) 


  
  * SelectionEnd (a _DateTime_ and equivalent to _SelectionRange.End_) 


  
  * SelectionRange (a _SelectionRange _containing members : ) 

    
      
    * End (a _DateTime_) 


      
    * Start (a _DateTime_) 

    
  


  
  * SelectionStart (a _DateTime_) 









#### The PictureBox Control





Properties :






  
  * ErrorImage (an _Image_) 


  
  * Image (an _Image_) 


  
  * ImageLocation (a path or URL) 


  
  * InitialImage (an _Image_) 


  
  * SizeMode (_Normal, StretchImage, AutoSize, CenterImage, Zoom_) 





#### The ImageList Component





_ImageList_ does not have a visual representation itself but is used to organize groups of images and can be used to supply images to other controls.





Properties :






  
  * ColorDepth (_Depth4Bit, Depth8Bit, Depth16Bit, Depth24Bit, Depth32Bit_) 


  
  * Images (an _ImageList.ImageCollection_ containing _Image_) 


  
  * ImageSize (a _Size_) 





### Lesson 3 : Configuring the WebBrowser Control and the NotifyIcon Component and Creating Access Keys





#### The WebBrowser Control





Properties :






  
  * AllowWebBrowserDrop 


  
  * CanGoBack 


  
  * CanGoForward 


  
  * Document 


  
  * DocumentStream 


  
  * DocumentText 


  
  * DocumentTitle 


  
  * DocumentType 


  
  * IsOffline 


  
  * IsWebBrowserContextMenuEnabled 


  
  * ScriptErrorSuppressed 


  
  * ScrollBarsEnabled 


  
  * URL 


  
  * WebBrowserShortcutsEnabled 





Operations :






  
  * GoBack 


  
  * GoForward 


  
  * GoHome 


  
  * GoSearch 


  
  * Navigate (raises _DocumentCompleted_ event) 


  
  * Print 


  
  * ShowPageSetupDialog 


  
  * ShowPrintDialog 


  
  * ShowPrintPreviewDialog 


  
  * ShowPropertiesDialog 


  
  * ShowSaveAsDialog 


  
  * Stop 





#### The NotifyIcon Component





Represents an Icon that appears in the system tray.





Properties :






  
  * BalloonTipIcon (_None, Info, Warning, Error_) 


  
  * BalloonTipText 


  
  * BalloonTipTitle 


  
  * ContextMenuStrip (a _ContextMenuStrip_) 


  
  * Icon 


  
  * Text 


  
  * Visible 





Operations :






  
  * ShowBalloonTrip(int secondsToDisplay) 





#### Creating an Access Key





A control must :






  
  * be capable of receiving the focus 


  
  * have a _Text_ property 


  
  * have a _UseMnemonic_ property (or if it doesn’t use a label beside the control) 









To create access key :






  
  * Set the _Text_ property 


  
  * Prepend the access key letter in _Text_ with &


  
  * Set _UseMnemonic = true_





## Chapter 4 : Tool Strips, Menus, and Events





### Lesson 1 : Configuring Tool Strips





#### The ToolStrip Control





Properties :






  
  * AllowItemReorder (allows item reordering when the user holds _Alt_) 


  
  * AllowMerge (whether two tools strips can be merged) 


  
  * CanOverflow (whether items automatically move to an overflow button) 


  
  * Dock (_None, Top, Bottom, Left, Right, Fill_) 


  
  * LayoutStyle (_HorizontalStackWithOverflow, VerticalStackWithOverflow, StackWithOverflow, Flow, Table)_


  
  * Items (a _ToolStripItemCollection_ containing _ToolStripItem _with members :) 

    
      
    * MergeAction (_Append, Insert, MatchOnly, Remove, Replace)_


      
    * MergeIndex (where an item will appear in _MergeAction = Insert_) 


      
    * ToolTipText 

    
  





The _ToolStripButton, ToolStripDropDownButton_ and _ToolStripSplitButton_ controls can display text, images or a combination of both with the following common properties :






  
    
    * DisplayStyle (_None, Text, Image, ImageAndText_) 


    
    * Image (an _Image_) 


    
    * ImageAlign (_ContentAlignment.Topleft, TopCenter, TopRight, MiddleLeft, MiddleCenter, MiddleRight, BottomLeft, BottomCenter, BottomRight_) 


    
    * ImageScaling (_None, SizeToFit_) 


    
    * ImageTransparentColor (a _Color_) 

  

  
  * RenderMode (_Custom, System, Professional, ManagerRenderMode_) 


  
  * ShowItemToolTips 


  
  * Stretch (whether tool strip stretches to a hosting _ToolStripContainer_) 


  
  * TextDirection (_Inherit, Horizontal, Vertical90, Vertical270_) 





#### Tool Strip Items





The items that can appear in a Tool Strip (_ToolStripItem_) have a number of derivations :






  
  * ToolStripLabel 
    
      
    * Text 


      
    * IsLink (if _true_ displays _Text_ similar to a _LinkLabel_) 

    
  


  
  * ToolStripButton 


  
  * ToolStripSeparator (can respond to mouse clicks) 


  
  * ToolStripComboBox 
    
      
    * DropStyle (_Simple, DropDown, DropDownList_) 

    
  


  
  * ToolStripTextBox (does not have a _MultiLine_ property!!) 


  
  * ToolStripProgressBar 
    
      
    * Minimum 


      
    * Maximum 


      
    * Value 


      
    * Style (_Blocks, Continuous, Marque_) 


      
    * MarqueAnimationSpeed (used whe _Style = Marque_) 


      
    * Increment() 


      
    * PerformStep() 

    
  


  
  * ToolStripDropDownButton 


  
  * ToolStripSplitButton (combines Button and DropDownButton to allow a clickable button that executes code as well as a drop down menu) 





#### The ToolStripContainer Control





Properties :






  
  * BottomToolStripPanel (a _ToolStripPanel. _Also Left, Right and Top versions) 


  
  * BottomToolStripPanelVisible (also Left, Right and Top versions) 


  
  * ContentPanel (a _ToolStripContentPanel_) 





#### The ToolStripManager static class





Operations :






  
  * FindToolStrip(string toolStripName) 


  
  * Merge(ToolStrip sourceToolStrip, ToolStrip targetToolStrip) 
    
      
    * The source tool strip _MergeAction_ property determines what happens (_Append, Insert, MatchOnly, Remove, Replace_) 


      
    * Both tool strips must have _AllowMerge = true_


      
    * Matching is performed on the _Text_ property 

    
  


  
  * IsShortcutDefined(Keys shortcut) 


  
  * IsValidShortcut(Keys shortcut) 


  
  * LoadSettings(Form targetForm, <string key>) 


  
  * SaveSettings(Form sourceForm, <string key>) 





### Lesson 2 : Creating and Configuring Menus





#### The MenuStrip : ToolStrip Control





A _ToolStrip_ but optimized for display of _ToolStripMenuItems ( : ToolStripDropDownItem)_ which have properties :






  
  * AutoSize (auto sized to fit _Text_) 


  
  * Checked 


  
  * CheckOnClick (CheckState is toggled each time the text is clicked) 


  
  * CheckState (_Checked, Unchecked, Indeterminate_) 


  
  * DisplayState (_None, Text, Image, ImageAndText_) 


  
  * DoubleClickEnabled 


  
  * DropDownItems (a _ToolStripItemCollection_) 


  
  * Enabled 


  
  * Visible 


  
  * Image 


  
  * MergeAction 


  
  * MergeIndex 


  
  * ShortcutKeyDisplayString (if _ShowShortcutKeys = true_ and this property is blank, the actual key combination will be displayed alongside the menu option.) 


  
  * ShortcutKeys 


  
  * ShowShortcutKeys 


  
  * Text 


  
  * TextImageRelation (_Overlay, ImageAboveText, TextAboveImage, ImageBeforeText, TextBeforeImage_) 





If you .Add() a menu item when it already exists in another then it will automatically be removed from the source menu strip. .Insert() will place it in a particular position.





#### The ContextMenuStrip : ToolStripDropDownMenu control





A context menu can be associated to another control if the target control exposes a _ContextMenuStrip_ property.





To create a copy of a menu item in the context menu use the _ToolStripItem_ constructor which accepts the text, image and event handler - in other words, manually create a copy :(





### Lesson 3 : Using Events and Event Handlers





#### Mouse Events






  
  * Click, DoubleClick, MouseEnter, MouseHover, MouseLeave have event sig : 
    
      
    * void Handler(object sender, EventArgs e) 

    
  


  
  * MouseClick, MouseDoubleClick, MouseDown, MouseMove, MouseUp, MouseWheel have event sig : 
    
      
    * void Handler(object sender, MouseEventArgs e) where args has props : 
        
          
      * Button (_None, Left, Right, Middle, XButton1, XButton2_) 


          
      * Clicks (an _int_) 


          
      * Delta (an _int_) 


          
      * Location (a _Point_) 


          
      * X 


          
      * Y 

        
      

    
  





#### Keyboard Events






  
  * KeyDown, KeyUp have event sig : 
    
      
    * void Handler(object sender, KeyEventArgs e) where args has props : 
        
          
      * Alt (a _bool_) 


          
      * Control(a _bool_) 


          
      * Handled(a _bool_) 


          
      * KeyCode (a _Keys_) 


          
      * KeyData (a _Keys_ which includes Alt, Ctrl or Shift) 


          
      * KeyValue (an _int_) 


          
      * Modifiers (a _Key_) 


          
      * Shift (a _bool_) 


          
      * SuppressKeyPress (a _bool_) 

        
      

    
  


  
  * KeyPress is raised for ASCII keypresses and has event sig : 
    
      
    * void Handler(object sender, KeyPressEventArgs e) where args has props : 
        
          
      * Handled (a _bool_) 


          
      * KeyChar (a _char_) 

        
      

    
  





## Chapter 5 : Configuring Connections and Connecting to Data





The classes in _System.Data_ are collectively known as ADO.NET.





### Lesson 1 : Creating and Configuring Connection Objects





Primary connection objects available in ADO.NET and which all inherit from _System.Data.Common.DbConnection _:






  
  * SqlConnection 


  
  * OleDbConnection (e.g. Access via Jet 4.0) 


  
  * OdbcConnection (e.g. a DSN) 


  
  * OracleConnection (Oracle 7.3, 8i or 9i) 





Common connection properties :






  
  * ConnectionString 


  
  * ConnectionTimeout (read only) 


  
  * Database (read only – the name of the database) 


  
  * DataSource (read only – the name of the database server) 


  
  * ServerVersion (read only) 


  
  * State (read only – _Closed, Open, Connecting, Executing, Fetching, Broken_) 





and operations :






  
  * BeginDbTransaction<IsolationLevel isolationLevel>) (_Unspecified, Chaos, ReadUncommitted, ReadCommitted, RepeatableRead, Serializable, Snapshot_) 


  
  * BeginTransaction(<IsolationLevel isolationLevel>) 


  
  * ChangeDatabase(string databaseName) 


  
  * Close() 


  
  * CreateCommand() (returns _DbCommand_) 


  
  * CreateDbCommand (returns _DbCommand_) 


  
  * EnlistTransaction (Transaction transaction) 


  
  * GetSchema(<string collectionName>, <string[] restrictionValues>) 


  
  * New 


  
  * OnStateChange(StateChangeEventArgs stateChange) 


  
  * Open 





















and events :






  
  * StateChange 


  
  * InfoMessage(object sender, SqlInfoMessageEventArgs e) where args has props : 
    
      
    * Errors (a _SqlErrorCollection _containing _SqlError_ with props :) 

        
          
      * Class (a _byte_) 


          
      * LineNumber (a _int)_


          
      * Message (a _string_) 


          
      * Number (a _int_) 


          
      * Procedure (a _string_) 


          
      * Server (a _string_) 


          
      * Source (a _string_) 


          
      * State (a _byte_) 

        
      


      
    * Message 


      
    * Source 

    
  





#### Creation Examples




    
    <span style="color:#2b91af;">SqlConnection </span>sqlConnection = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlConnection</span>(<span style="color:#a31515;">"Data Source=ServerName;Initial Catalog=DatabaseName;Integrated Security=True"</span>);
    
    <span style="color:#2b91af;">OleDbConnection </span>oledbConnection = <span style="color:blue;">new </span><span style="color:#2b91af;">OleDbConnection</span>(<span style="color:#a31515;">"Provider=Microsoft.Jet.OLEDB.4.0;Data Source=\"Nwind.mdb\";Persist Security Info=False"</span>);
    
    <span style="color:#2b91af;">OdbcConnection </span>odbcConnection = <span style="color:blue;">new </span><span style="color:#2b91af;">OdbcConnection</span>(<span style="color:#a31515;">"Dsn=MS Access Database;dbq=C:\\DataSource;driverid=281;fil=MS Access;maxbuffersize=2048;pagetimeout=5;uid=admin"</span>);
    
    <span style="color:#2b91af;">OracleConnection </span>oracleConnection = <span style="color:blue;">new </span><span style="color:#2b91af;">OracleConnection</span>(<span style="color:#a31515;">"Data Source=Oracle8i;Integrated Security=yes"</span>);


[](http://11011.net/software/vspaste)



### Lesson 2 : Connecting to Data Using Connection Objects





…





### Lesson 3 : Working with Connection Pools









Connections are pooled by default according to the connection string properties and security information and are separated by :






  
  * process 


  
  * application domain 


  
  * connection string 


  
  * and identity if integrated security is being used 





Connection string keywords relevant for SQL and Oracle connection pooling :






  
  * Connection Lifetime (default 0 in seconds. 0 is the maximum timeout) 


  
  * Connection Reset (**not oracle **default _true_ – connection is reset when drawn from the pool. Set to _false_ for SQL Server 7.0 to avoid a server round trip) 


  
  * Enlist (default_ true_) 


  
  * Load Balance Timeout (**not oracle **default 0 in seconds – the minimum time to live in the pool) 


  
  * Max Pool Size (default 100) 


  
  * Min Pool Size (default 0) 


  
  * Pooling (default _true/yes_) 





and for OLE DB connection pooling :






  
  * OLE DB Services (where the value specified is :) 
    
      
    * -1 (all services on) 


      
    * -4 (all services except _Pooling_ and _AutoEnlistment_) 


      
    * -5 (all services except _Client Cursor_) 


      
    * -8 (all services except _Pooling, AutoEnlistment, _and _Client Cursor_) 


      
    * 0 (no services) 

    
  





Connection methods for pools :






  
  * ClearAllPools (Sql and Oracle – empties all pools for the provider) 


  
  * ClearPool (Sql and Oracle – empties the pool for a particular connection string) 


  
  * ReleaseObjectPool (OleDb and Odbc – can release the pool when the last connection is released) 

























### Lesson 4 : Handling Connection Errors






  
  * SqlException has props : 
    
      
    * Errors (a _SqlErrorCollection_ see InfoMessage event above for details) 


      
    * LineNumber 


      
    * Number 


      
    * Procedure 


      
    * Server 


      
    * Source 


      
    * State 

    
  





### Lesson 5 : Enumerating the Available SQL Servers on a Network









The _SqlDataSourceEnumerator.GetDataSources_ returns a _DataTable_ the following Schema detailing information about SQL Servers visible on the network :






  
  * ServerName 


  
  * InstanceName 


  
  * IsClustered 


  
  * Version 





The SQL Browser must be running to see a SQL instance on a machine and port 1433 must be open. Network traffic and timeouts may affect the list of servers returned.





### Lesson 6 : Securing Sensitive Connection String Data





It is recommended to set the _Persist Security Information_ connection string option to _false. _Integrated security can be turned on in the connection string by setting the following provider specific options :






  
  * Integrated Security=True (SqlClient) 


  
  * Integrated Security=SSPI (SqlClient and OleDb) 


  
  * Trusted_Connection=Yes (Odbc) 


  
  * Integrated Security=Yes (OracleClient) 





#### Securing Data in Configuration Files




    
    <span style="color:#2b91af;">Configuration </span>config = <span style="color:#2b91af;">ConfigurationManager</span>.OpenExeConfiguration(<span style="color:#2b91af;">ConfigurationUserLevel</span>.None);
    <span style="color:#2b91af;">ConfigurationSection </span>connStrings = config.ConnectionStrings;
    
    <span style="color:green;">// Encrypt the section
    </span>connStrings.SectionInformation.ProtectSection(<span style="color:#a31515;">"DataProtectionConfigurationProvider"</span>;
    
    <span style="color:green;">// Decrypt the section
    </span>connStrings.SectionInformation.UnprotectSection();
    
    connStrings.SectionInformation.ForceSave = <span style="color:blue;">true</span>;
    config.Save(<span style="color:#2b91af;">ConfigurationSaveMode</span>.Full);


[](http://11011.net/software/vspaste)



## Chapter 6 : Working with Data in a Connected Environment





### Lesson 1 : Creating and Executing Command Objects





There is a _Command_ for each provider :






  
  * SqlCommand 


  
  * OleDbCommand 


  
  * OdbcCommand 


  
  * OracleCommand 





Each have the following properties :






  
  * CommandText (sql statement or stored procedure name to execute. Multiple statements should be separated by semicolons) 


  
  * CommandTimeout 


  
  * CommandType (_Text, StoredProcedure_) 


  
  * Connection 


  
  * Parameters (a _SqlParameterCollection_ containing _SqlParameter_ with members : ) 

    
      
    * DbType (_AnsiString, Binary, Byte, Boolean, Currency Date, DateTime, Decimal, Double, Guid, Int16, Int32, Int64, Object, SByte, Single, String, Time, UIn16, UInt32, UInt64, VarNumeric, AnsiStringFixedLength, StringFixedLength, Xml, DateTime2, DateTimeOffset_) 


      
    * Direction (_Input, Output, InputOutput_) 


      
    * IsNullable 


      
    * ParameterName 


      
    * Precision 


      
    * Scale 


      
    * Size 


      
    * SourceColumn 


      
    * SourceColumnNullMapping 


      
    * SourceVersion (a _DataRowVersion – Original, Current Proposed, Default_) 


      
    * SqlDbType 


      
    * SqlValue 


      
    * TypeName 


      
    * UdtTypeName 


      
    * Value 

    
  


  
  * Transaction (a _SqlTransaction_) 





and operations :






  
  * Cancel 


  
  * ExecuteNonQuery (no return data) 


  
  * ExecuteReader (return tabular data. With members :) 
    
      
        
      * NextResult() (advances to the next result set when executing multiple statements returning _True _if another exists) 

      

      
    * Read (advances to the next record returning _True_ if one exists) 


      
    * GetXXX(int column) (returns a value of type XXX from the zero-based column index) 

    
  


  
  * ExecuteScalar (returns the first column of the first row or the stored procedure return data as an _**Object**_) 


  
  * ExecuteXMLReader (returns a _System.Xml.XmlReader_) 


  
  * BeginExecuteNonQuery 


  
  * BeginExecuteReader 


  
  * BeginExecuteXmlReader 


  
  * EndExecuteNonQuery 


  
  * EndExecuteReader 


  
  * EndExecuteXMLReader 





and events :






  
  * Disposed 


  
  * StatementCompleted (_SqlCommand _only) 





### Lesson 2 : Working with Parameters in SQL Commands





Parameter types are all defined in _System.Data.SqlDbType_. Name parameters are prefixed with an @ when using SQL Server whilst all others use a question mark (?) as a parameter placeholder and order is used to assign parameters.





### Lesson 3 : Saving and Retrieving BLOB Values in a Database





Setting the _CommandBehaviour = SequentialAccess_ on a _DataReader_ allows you to call _GetBytes()_ and stream BLOBs from the database.





Here’s code to retrieve an image from SQL Server :




    
    <span style="color:#2b91af;">SqlCommand </span>command = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlCommand</span>(<span style="color:#a31515;">"SELECT pub_id, logo FROM pub_info"</span>, connection);
    
    <span style="color:#2b91af;">FileStream </span>stream;      <span style="color:green;">// Writes the BLOB to a file (*.bmp).
    </span><span style="color:#2b91af;">BinaryWriter </span>writer;    <span style="color:green;">// Streams the BLOB to the FileStream object.
    
    </span><span style="color:blue;">int </span>bufferSize = 100;                   <span style="color:green;">// Size of the BLOB buffer.
    </span><span style="color:blue;">byte</span>[] outByte = <span style="color:blue;">new byte</span>[bufferSize];  <span style="color:green;">// The BLOB byte[] buffer to be filled by GetBytes.
    </span><span style="color:blue;">long </span>retval;                            <span style="color:green;">// The bytes returned from GetBytes.
    </span><span style="color:blue;">long </span>startIndex = 0;                    <span style="color:green;">// The starting position in the BLOB output.
    </span><span style="color:blue;">string </span>pubID = <span style="color:#a31515;">""</span>;                      <span style="color:green;">// The publisher id to use in the file name.
    
    </span>connection.Open();
    <span style="color:#2b91af;">SqlDataReader </span>reader = command.ExecuteReader(<span style="color:#2b91af;">CommandBehavior</span>.SequentialAccess);
    <span style="color:blue;">while </span>(reader.Read())
    {
        pubID = reader.GetString(0);        <span style="color:green;">// Get the publisher id, which must occur before getting the logo.
        </span>stream = <span style="color:blue;">new </span><span style="color:#2b91af;">FileStream</span>(<span style="color:#a31515;">"logo" </span>+ pubID + <span style="color:#a31515;">".bmp"</span>, <span style="color:#2b91af;">FileMode</span>.OpenOrCreate, <span style="color:#2b91af;">FileAccess</span>.Write);
        writer = <span style="color:blue;">new </span><span style="color:#2b91af;">BinaryWriter</span>(stream);
        startIndex = 0;                     <span style="color:green;">// Reset the starting byte for the new BLOB.
    
        // Read bytes into outByte[] and retain the number of bytes returned.
        </span>retval = reader.GetBytes(1, startIndex, outByte, 0, bufferSize);
        <span style="color:blue;">while </span>(retval == bufferSize)        <span style="color:green;">// Continue while there are bytes beyond the size of the buffer.
        </span>{
            writer.Write(outByte);
            writer.Flush();
            startIndex += bufferSize;       <span style="color:green;">// Reposition start index to end of last buffer and fill buffer.
            </span>retval = reader.GetBytes(1, startIndex, outByte, 0, bufferSize);
        }
        writer.Write(outByte, 0, (<span style="color:blue;">int</span>)retval - 1);   <span style="color:green;">// Write the remaining buffer.
        </span>writer.Flush();         
        writer.Close();
        stream.Close();
    }
    reader.Close();
    connection.Close();


[](http://11011.net/software/vspaste)



#### ‘Chunking’ data updates to BLOBs





BLOBs can be streamed into the database in much the same way as any other data. In the example below photo is a _byte[]_ and the data type has been selected appropriately :




    
    command.Parameters.Add(<span style="color:#a31515;">"@Photo"</span>, <span style="color:#2b91af;">SqlDbType</span>.Image, photo.Length).Value = photo;


[](http://11011.net/software/vspaste)



However, this method can be very intensive therefore there is method of ‘chunking’ the data to reduce the need for resources.The code below has the following features :






  
  * TEXTPTR() sql function is used to retrieve a pointer to a field 


  
  * UPDATETEXT sql is used to insert a byte array at a particular offset 




    
    <span style="color:blue;">public static int </span>AddEmployee(<span style="color:blue;">string </span>lastName, <span style="color:blue;">string </span>firstName, <span style="color:blue;">string </span>title, <span style="color:#2b91af;">DateTime </span>hireDate, <span style="color:blue;">int </span>reportsTo, <span style="color:blue;">string </span>photoFilePath)
    {
        <span style="color:blue;">using </span>(<span style="color:#2b91af;">SqlConnection </span>connection = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlConnection</span>(<span style="color:#a31515;">"Data Source=(local);Integrated Security=true;Initial Catalog=Northwind;"</span>))
        {
            <span style="color:#2b91af;">SqlCommand </span>addEmp = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlCommand</span>(
                <span style="color:#a31515;">"INSERT INTO Employees (LastName, FirstName, Title, HireDate, ReportsTo, Photo) " </span>+
                <span style="color:#a31515;">"Values(@LastName, @FirstName, @Title, @HireDate, @ReportsTo, 0x0);" </span>+
                <span style="color:#a31515;">"SELECT @Identity = SCOPE_IDENTITY();" </span>+
                <span style="color:#a31515;">"SELECT @Pointer = TEXTPTR(Photo) FROM Employees WHERE EmployeeID = @Identity"</span>, 
                connection);
    
            addEmp.Parameters.Add(<span style="color:#a31515;">"@LastName"</span>, <span style="color:#2b91af;">SqlDbType</span>.NVarChar, 20).Value = lastName;
            addEmp.Parameters.Add(<span style="color:#a31515;">"@FirstName"</span>, <span style="color:#2b91af;">SqlDbType</span>.NVarChar, 10).Value = firstName;
            addEmp.Parameters.Add(<span style="color:#a31515;">"@Title"</span>, <span style="color:#2b91af;">SqlDbType</span>.NVarChar, 30).Value = title;
            addEmp.Parameters.Add(<span style="color:#a31515;">"@HireDate"</span>, <span style="color:#2b91af;">SqlDbType</span>.DateTime).Value = hireDate;
            addEmp.Parameters.Add(<span style="color:#a31515;">"@ReportsTo"</span>, <span style="color:#2b91af;">SqlDbType</span>.Int).Value = reportsTo;
    
            <span style="color:#2b91af;">SqlParameter </span>idParm = addEmp.Parameters.Add(<span style="color:#a31515;">"@Identity"</span>, <span style="color:#2b91af;">SqlDbType</span>.Int);
            idParm.Direction = <span style="color:#2b91af;">ParameterDirection</span>.Output;
            <span style="color:#2b91af;">SqlParameter </span>ptrParm = addEmp.Parameters.Add(<span style="color:#a31515;">"@Pointer"</span>, <span style="color:#2b91af;">SqlDbType</span>.Binary, 16);
            ptrParm.Direction = <span style="color:#2b91af;">ParameterDirection</span>.Output;
    
            connection.Open();
            addEmp.ExecuteNonQuery();
            <span style="color:blue;">int </span>newEmpID = (<span style="color:blue;">int</span>)idParm.Value;
            StorePhoto(photoFilePath, (<span style="color:blue;">byte</span>[])ptrParm.Value, connection);
            <span style="color:blue;">return </span>newEmpID;
        }
    }


[](http://11011.net/software/vspaste)


    
    <span style="color:blue;">public static void </span>StorePhoto(<span style="color:blue;">string </span>fileName, <span style="color:blue;">byte</span>[] pointer, <span style="color:#2b91af;">SqlConnection </span>connection)
    {
        <span style="color:blue;">int </span>bufferLen = 128;    <span style="color:green;">// The size of the "chunks" of the image.
        </span><span style="color:#2b91af;">SqlCommand </span>appendToPhoto = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlCommand</span>(<span style="color:#a31515;">"UPDATETEXT Employees.Photo @Pointer @Offset 0 @Bytes"</span>, connection);
        <span style="color:#2b91af;">SqlParameter </span>ptrParm = appendToPhoto.Parameters.Add(<span style="color:#a31515;">"@Pointer"</span>, <span style="color:#2b91af;">SqlDbType</span>.Binary, 16);
        ptrParm.Value = pointer;
        <span style="color:#2b91af;">SqlParameter </span>photoParm = appendToPhoto.Parameters.Add(<span style="color:#a31515;">"@Bytes"</span>, <span style="color:#2b91af;">SqlDbType</span>.Image, bufferLen);
        <span style="color:#2b91af;">SqlParameter </span>offsetParm = appendToPhoto.Parameters.Add(<span style="color:#a31515;">"@Offset"</span>, <span style="color:#2b91af;">SqlDbType</span>.Int);
        offsetParm.Value = 0;
    
        <span style="color:green;">// Read the image in and write it to the database 128 (bufferLen) bytes at a time.
        // Tune bufferLen for best performance. Larger values write faster, but
        // use more system resources.
        </span><span style="color:#2b91af;">FileStream </span>fs = <span style="color:blue;">new </span><span style="color:#2b91af;">FileStream</span>(fileName, <span style="color:#2b91af;">FileMode</span>.Open, <span style="color:#2b91af;">FileAccess</span>.Read);
        <span style="color:#2b91af;">BinaryReader </span>br = <span style="color:blue;">new </span><span style="color:#2b91af;">BinaryReader</span>(fs);
        <span style="color:blue;">byte</span>[] buffer = br.ReadBytes(bufferLen);
        <span style="color:blue;">int </span>offset_ctr = 0;
        <span style="color:blue;">while </span>(buffer.Length > 0)
        {
            photoParm.Value = buffer;
            appendToPhoto.ExecuteNonQuery();
            offset_ctr += bufferLen;
            offsetParm.Value = offset_ctr;
            buffer = br.ReadBytes(bufferLen);
        }
        br.Close();
        fs.Close();
    }





### Lesson 4 : Performing Bulk Copy Operations





The basic process for bulk copying data between tables is :






  
  * Read the source data using a DataReader. 


  
  * Open the destination connection 


  
  * Create a _SqlBulkCopy_ passing the destination connection into the constructor. A _SqlTransaction_ can also be specified to enrol the bulk copy operation in another transaction. 


  
  * Set the _SqlBulkCopy.BatchSize_ which determines the number of rows updated in each batch. If an error occurs and a transaction has not be started then only the last batch will be rolled back if _UseInternalTransaction_ was set on the _SqlBulkCopy_ constructor 


  
  * Set the _SqlBulkCopy_._DestinationTableName_


  
  * Call _SqlBulkCopy.ColumnMappings.Clear()_


  
  * Call _SqlBulkCopy.ColumnMappings.Add(src, dest)_ for each column to be mapped 


  
  * Call _SqlBulkCopy.WriterToServer(reader)_


  
  * Repeat the last four steps if there are multiple DataReaders to write! 





.Net 1.1 and earlier do not support _SqlBulkCopy_ so you can call the T-SQL _BULK INSERT_ by using a _SqlCommand :_




    
    <span style="color:blue;">using </span>(<span style="color:#2b91af;">SqlConnection </span>connection = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlConnection</span>(connectionString))
    {
        <span style="color:blue;">string </span>queryString = <span style="color:#a31515;">@"BULK INSERT Northwind.dbo.[Order Details] " </span>+
            <span style="color:#a31515;">@"FROM 'f:\mydata\data.tbl' " </span>+
            <span style="color:#a31515;">@"WITH ( FORMATFILE='f:\mydata\data.fmt' )"</span>;
        connection.Open();
        <span style="color:#2b91af;">SqlCommand </span>command = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlCommand</span>(queryString, connection);
        command.ExecuteNonQuery();
    }





### Lesson 5 : Performing Transactions by Using the Transaction Object





A transaction can be started by calling Connection._BeginTransaction(<IsolationLevel>) _or joined by calling Connection._EnlistTransaction(activeTransaction)_. Sql_Transaction_ has the properties :






  
  * Connection (a _SqlConnection_) 


  
  * DbConnection (a _DbConnection_) 


  
  * IsolationLevel 





and operations :






  
  * Commit() 


  
  * Rollback(<string transactionName>) 


  
  * Save(string savePointName) 





The available IsolationLevels are :






  
  * Chaos (pending changes from highly isolated transactions cannot be overwritten) 


  
  * ReadCommitted (shared locks are held while the data is being read to avoid dirty reads, but the data can be changed before the end of the transaction, resulting in non-repeatable reads of phantom data) 


  
  * ReadUncommitted (a dirty read is possible) 


  
  * RepeatableRead (locks are placed on all data used in a query preventing non-repeatable reads but phantom rows still possible) 


  
  * Serializable (a range lock is placed on the DataSet preventing other users from updating or inserting rows into the dataset until the transaction is complete) 


  
  * Snapshot (reduces blocking by storing a version of data that one application can read while another is modifying the same data. Indicates that from one transaction you cannot see changes made in other transactions, even if you requery.) 


  
  * Unspecified (a different isolation level than the one specified is being used, but the level cannot be determined) 





## Chapter 7 : Create, Add, Delete, and Edit Data in a Disconnected Environment





### [![untitled](http://philiphendry.files.wordpress.com/2008/07/untitled-thumb.png)](http://philiphendry.files.wordpress.com/2008/07/untitled.png)





### Lesson 1 : Creating DataSet Objects





#### The DataSet Object





Untyped _DataSet_ objects are created manually with _DataTable_ objects added to its _Tables_ collection (and optional _DataRelation_) whilst typed _DataSet_ dervice from a schema (.xsd)





### Lesson 2 : Creating DataTable Objects





#### Merging DataSet Contents





To merge a _DataSet_ (or _DataTable_) with an existing _DataSet_ call _Merge()_. The _PreserveChanges_ flag indicates whether to preserve changes in the source data set and overwrite the target (matching on the primary key.) 




    
    ds.Merge(sourceDS, <span style="color:blue;">true</span>, <span style="color:#2b91af;">MissingSchemaAction</span>.Add);


[](http://11011.net/software/vspaste)



The _MissingSchemaAction_ defines what should happen if the schemas differ :






  
  * Add (default – all schema items in the source DataSet are added to the target) 


  
  * AddWithKey (all schema items and primary key settings are added to the target) 


  
  * Error (an exception will be thrown if the schema is different) 


  
  * Ignore (all schema differences are ignored) 





#### Example Code to Demonstrate DataSet Features





In the example below the creation of a _DataRelation_ automatically generates a _ForeignKeyConstraint_ and a _UniqueConstraint_.




    
    <span style="color:#2b91af;">DataTable </span>ordersTable = <span style="color:blue;">new </span><span style="color:#2b91af;">DataTable</span>(<span style="color:#a31515;">"orders"</span>);
    <span style="color:#2b91af;">DataTable </span>orderDetailsTable = <span style="color:blue;">new </span><span style="color:#2b91af;">DataTable</span>(<span style="color:#a31515;">"orderDetails"</span>);
    
    <span style="color:#2b91af;">DataSet </span>ds = <span style="color:blue;">new </span><span style="color:#2b91af;">DataSet</span>(<span style="color:#a31515;">"myDataSet"</span>);
    ds.Tables.Add(ordersTable);
    ds.Tables.Add(orderDetailsTable);
    
    <span style="color:green;">// Create an auto-incrementing OrderId column
    </span>ordersTable.Columns.Add(<span style="color:#a31515;">"OrderId"</span>, <span style="color:#2b91af;">Type</span>.GetType(<span style="color:#a31515;">"System.Int32"</span>));
    ordersTable.Columns[<span style="color:#a31515;">"OrderId"</span>].AutoIncrement = <span style="color:blue;">true</span>;
    ordersTable.Columns[<span style="color:#a31515;">"OrderId"</span>].AutoIncrementSeed = 100;
    ordersTable.Columns[<span style="color:#a31515;">"OrderId"</span>].AutoIncrementStep = 5;
    
    <span style="color:green;">// An example of adding a column expression
    </span>orderDetailsTable.Columns.Add(<span style="color:#a31515;">"TotalPrice"</span>, <span style="color:#2b91af;">Type</span>.GetType(<span style="color:#a31515;">"System.Double"</span>), <span style="color:#a31515;">"UnitPrice * Quantity"</span>);
    
    <span style="color:green;">// Some more examples of column properties
    </span>orderDetailsTable.Columns.Add(<span style="color:#a31515;">"OrderId"</span>, <span style="color:#2b91af;">Type</span>.GetType(<span style="color:#a31515;">"System.Int32"</span>));
    orderDetailsTable.Columns.Add(<span style="color:#a31515;">"OrderDetailId"</span>, <span style="color:#2b91af;">Type</span>.GetType(<span style="color:#a31515;">"System.Int32"</span>));
    orderDetailsTable.Columns[<span style="color:#a31515;">"OrderDetailId"</span>].AllowDBNull = <span style="color:blue;">false</span>;
    orderDetailsTable.Columns[<span style="color:#a31515;">"OrderDetailId"</span>].Unique = <span style="color:blue;">true</span>;
    
    <span style="color:green;">// Creating a relationship (column data types must match)
    </span><span style="color:#2b91af;">DataRelation </span>relation = <span style="color:blue;">new </span><span style="color:#2b91af;">DataRelation</span>(<span style="color:#a31515;">"Order_OrderDetails"</span>,
        ordersTable.Columns[<span style="color:#a31515;">"OrderId"</span>],
        orderDetailsTable.Columns[<span style="color:#a31515;">"OrderId"</span>]);
    ds.Relations.Add(relation);
    
    <span style="color:green;">// The relationship can be found on both the child and parent tables
    </span><span style="color:#2b91af;">DataRelation </span>childRelation = ordersTable.ChildRelations[<span style="color:#a31515;">"Order_OrderDetails"</span>];
    <span style="color:#2b91af;">DataRelation </span>parentRelation = orderDetailsTable.ParentRelations[<span style="color:#a31515;">"Order_OrderDetails"</span>];
    
    <span style="color:green;">// Now navigate the relationship using either the object or relationship name
    </span><span style="color:#2b91af;">DataRow</span>[] orderDetailRows = ordersTable.Rows[0].GetChildRows(relation);
    <span style="color:#2b91af;">DataRow </span>orderRow = orderDetailsTable.Rows[0].GetParentRow(<span style="color:#a31515;">"Order_OrderDetails"</span>);
    
    <span style="color:green;">// ALternative method for setting unique key constraint. There are
    // also a variety of overloads for .Add() that offer quicker ways
    // to add the constraint features (e.g. name, data column and primary key bool)
    </span><span style="color:#2b91af;">UniqueConstraint </span>unique = <span style="color:blue;">new </span><span style="color:#2b91af;">UniqueConstraint</span>(ordersTable.Columns[<span style="color:#a31515;">"OrderId"</span>], <span style="color:blue;">true</span>);
    <span style="color:blue;">bool </span>isPrimary = unique.IsPrimaryKey; <span style="color:green;">// The primary key boolean is passed in the constructor
    </span>unique.ConstraintName = <span style="color:#a31515;">"PK_Orders_OrderId"</span>;
    ordersTable.Constraints.Add(unique);
    
    <span style="color:green;">// Create a Foreign Key between two tables which can be used to force a constraint
    // between them. The DataColumn parameters can also be DataColumn[] if using a composite key
    </span><span style="color:#2b91af;">ForeignKeyConstraint </span>foreignKey = <span style="color:blue;">new </span><span style="color:#2b91af;">ForeignKeyConstraint</span>(<span style="color:#a31515;">"FK_Orders_OrderDetails"</span>,
        ordersTable.Columns[<span style="color:#a31515;">"OrderId"</span>],
        orderDetailsTable.Columns[<span style="color:#a31515;">"OrderId"</span>]);
    foreignKey.DeleteRule = <span style="color:#2b91af;">Rule</span>.Cascade; <span style="color:green;">// or None, SetDefault, SetNull
    </span>foreignKey.UpdateRule = <span style="color:#2b91af;">Rule</span>.None;
    foreignKey.AcceptRejectRule = <span style="color:#2b91af;">AcceptRejectRule</span>.Cascade; <span style="color:green;">// or None (enforced when calling AcceptChanges())
    </span>ordersTable.Constraints.Add(foreignKey);


[](http://11011.net/software/vspaste)























#### Lesson 3 : Creating DataAdapter Objects





_DataAdapter_ objects are provider-specific and contain connection information and select, update, insert and delete commands to fill _DataTable_ objects and make updates.




    
    <span style="color:#2b91af;">SqlConnection </span>conn;
    
    <span style="color:green;">// An example SqlDataAdapter that manually creates each SqlCommand
    </span><span style="color:#2b91af;">SqlDataAdapter </span>adapter = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlDataAdapter</span>(<span style="color:#a31515;">"SELECT * FROM Customers"</span>, conn);
    adapter.InsertCommand = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlCommand</span>(<span style="color:#a31515;">"INSERT INTO ..."</span>, conn);
    adapter.UpdateCommand = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlCommand</span>(<span style="color:#a31515;">"UPDATE ... SET ..."</span>, conn);
    adapter.DeleteCommand = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlCommand</span>(<span style="color:#a31515;">"DELETE FROM ..."</span>, conn);
    <span style="color:green;">// now add any necessary parameters to each SqlCommand parameters collection
    
    // If the select returns data from a single table and the result set contains
    // either a primary key or unique column then the SqlCommandBuilder() can
    // generate the insert, update and delete automatically
    </span><span style="color:#2b91af;">SqlDataAdapter </span>adapter2 = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlDataAdapter</span>(<span style="color:#a31515;">"SELECT * FROm Customers"</span>, conn);
    <span style="color:#2b91af;">SqlCommandBuilder </span>commands = <span style="color:blue;">new </span><span style="color:#2b91af;">SqlCommandBuilder</span>(adapter2);
    
    <span style="color:green;">// To improve performance commands can be sent to the database in batches
    // The RowUpdating event fires for each row whilst RowUpdated fires once for the batch
    </span>adapter.UpdateBatchSize = 5;
    
    <span style="color:green;">// Action to take if the mappings don't match
    </span>adapter.MissingMappingAction = <span style="color:#2b91af;">MissingMappingAction</span>.Passthrough; <span style="color:green;">// or Error, Ignore
    
    // Action to take if a schema change has occured in the db when the select runs
    </span>adapter.MissingSchemaAction = <span style="color:#2b91af;">MissingSchemaAction</span>.Add; <span style="color:green;">// or AddWithKey, Error, None</span>


[](http://11011.net/software/vspaste)



#### Filling a DataTable Using an ADO Recordset




    
    <span style="color:#2b91af;">String </span>northwindConnectionString = <span style="color:#a31515;">"Provider=SQLOLEDB;Data Source=.\\sqlexpress;Integrated Security=SSPI;Initial Catalog=Northwind"</span>;
    ADODB.<span style="color:#2b91af;">Recordset </span>rs = <span style="color:blue;">new </span>ADODB.<span style="color:#2b91af;">Recordset</span>();
    rs.Open(<span style="color:#a31515;">"SELECT * FROM Customers"</span>, northwindConnectionString, ADODB.<span style="color:#2b91af;">CursorTypeEnum</span>.adOpenStatic, ADODB.<span style="color:#2b91af;">LockTypeEnum</span>.adLockBatchOptimistic, 0);
    
    <span style="color:#2b91af;">DataTable </span>customersTable = <span style="color:blue;">new </span><span style="color:#2b91af;">DataTable</span>(<span style="color:#a31515;">"Customers"</span>);
    System.Data.OleDb.<span style="color:#2b91af;">OleDbDataAdapter </span>adapter = <span style="color:blue;">new </span>System.Data.OleDb.<span style="color:#2b91af;">OleDbDataAdapter</span>();
    adapter.Fill(customersTable, rs);


[](http://11011.net/software/vspaste)











### Lesson 4 : Working with Data in DataTable Objects




    
    <span style="color:green;">// Typed DataSets provide named properties and tables...
    </span><span style="color:#2b91af;">NorthwindDataSet</span>.<span style="color:#2b91af;">CustomersRow </span>newRow = (<span style="color:#2b91af;">NorthwindDataSet</span>.<span style="color:#2b91af;">CustomersRow</span>)northwindDataSet1.Customers.NewRow();
    newRow.CustomerID = <span style="color:#a31515;">"WINGT"</span>;
    newRow.CompanyName = <span style="color:#a31515;">"Wingtop Toys"</span>;
    newRow.ContactName = <span style="color:#a31515;">"Somebody"</span>;
    northwindDataSet1.Customers.Rows.Add(newRow);   <span style="color:green;">// state is Added
    </span>newRow.AcceptChanges();                         <span style="color:green;">// state is now unchanged
    
    // The row state can be :
    //      Unchanged (no changes since AcceptChanges() or Fill() calls)
    //      Added (added since last AcceptChanges())
    //      Modified (updated since last AcceptChanges())
    //      Deleted (deleted since last AcceptChanges())
    //      Detached (not yet been added to a DataTable.Rows collection)
    </span><span style="color:#2b91af;">DataRowState </span>state = newRow.RowState;           <span style="color:green;">// state is Unchanged
    </span>newRow.Delete();                                <span style="color:green;">// state is now deleted
    </span>newRow.AcceptChanges(); <span style="color:green;">// also available on DataTable and DataSet
                                                    // state is now detached
    //newRow.RejectChanges();
    
    // Check and access row errors
    </span><span style="color:blue;">if </span>(northwindDataSet1.Customers.HasErrors)
    {
        <span style="color:#2b91af;">DataRow</span>[] erroredRows = northwindDataSet1.Customers.GetErrors();
        <span style="color:#2b91af;">Console</span>.WriteLine(erroredRows[0].RowError);
        erroredRows[0].RowError = <span style="color:#a31515;">""</span>; <span style="color:green;">// Set error to 'processed'
    </span>}


[](http://11011.net/software/vspaste)



[](http://11011.net/software/vspaste)There are a number of events :






  
  * ColumnChanged 


  
  * ColumnChanging 


  
  * RowChanged 


  
  * RowChanging 


  
  * RowDeleted 


  
  * RowDeleting 





### Lesson 5 : Working with XML in DataSet Objects





to be completed









### Lesson 6 : Creating and Using DataView Objects





to be completed (don’t forget to look up the DataViewManager)





## Chapter 8 : Implementing Data-Bound Controls

















### Lesson 1 : Creating a Data-Bound Form with the Data Sources Wizard





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb13.png)](http://philiphendry.files.wordpress.com/2008/07/image13.png)





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb14.png)](http://philiphendry.files.wordpress.com/2008/07/image14.png)





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb15.png)](http://philiphendry.files.wordpress.com/2008/07/image15.png)





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb16.png)](http://philiphendry.files.wordpress.com/2008/07/image16.png)





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb17.png)](http://philiphendry.files.wordpress.com/2008/07/image17.png)





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb18.png)](http://philiphendry.files.wordpress.com/2008/07/image18.png)





### Lesson 2 : Implementing Data-Bound Controls

































#### Simple Data-Binding





The following code would bind the textbox _Text_ property to the _ordersBindingSource.CustomerID_. For this example, the _ordersBindingSource_ was created automatically by the Data Set Configuration Wizard.




    
    <span style="color:#2b91af;">TextBox </span>textbox = <span style="color:blue;">new </span><span style="color:#2b91af;">TextBox</span>();
    <span style="color:blue;">this</span>.Controls.Add(textbox);
    textbox.TextChanged += <span style="color:blue;">delegate</span>(<span style="color:blue;">object </span>s, <span style="color:#2b91af;">EventArgs </span>a) { <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#2b91af;">String</span>.Format(<span style="color:#a31515;">"Textbox changed to {0}"</span>, textbox.Text)); };
    textbox.DataBindings.Add(<span style="color:#a31515;">"Text"</span>, ordersBindingSource, <span style="color:#a31515;">"CustomerID"</span>);


[](http://11011.net/software/vspaste)



#### Complex Data Binding





Complex binding binds more than one element of data to more than one property of a control. The following example uses the BendingNavigator toolstrip :





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb19.png)](http://philiphendry.files.wordpress.com/2008/07/image19.png)




    
    <span style="color:#2b91af;">BindingNavigator </span>nav = <span style="color:blue;">new </span><span style="color:#2b91af;">BindingNavigator</span>(customersBindingSource);
    <span style="color:blue;">this</span>.Controls.Add(nav);
    nav.Dock = <span style="color:#2b91af;">DockStyle</span>.Top;
    
    <span style="color:#2b91af;">DataGrid </span>datagrid = <span style="color:blue;">new </span><span style="color:#2b91af;">DataGrid</span>();
    <span style="color:blue;">this</span>.Controls.Add(datagrid);
    datagrid.Dock = <span style="color:#2b91af;">DockStyle</span>.Fill;
    datagrid.DataSource = customersBindingSource;
    
    <span style="color:green;">// Or without the BindingNavigator you can use the data set...
    //datagrid.DataSource = northwindDataSet1;
    //datagrid.DataMember = "Customers";</span>


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)



#### The BindingSource Class





The _BindingSource_ class provides a level of indirection such that the source of binding to a control can be changed quickly without having to modify all controls :




    
    <span style="color:#2b91af;">BindingSource </span>customersBinding = <span style="color:blue;">new </span><span style="color:#2b91af;">BindingSource</span>();
    customersBindingSource.DataSource = northwindDataSet1;
    customersBindingSource.DataMember = <span style="color:#a31515;">"Customers"</span>;


[](http://11011.net/software/vspaste)



















### Lesson 3 : Working with the DataGridView





The _DataGridView_ replaces and adds functionality to _DataGrid_ which remains for backward compatibility. It can bind to :






  
  * IList 


  
  * IListSource (e.g. _DataTable_ and _DataSet_) 


  
  * IBindingList 


  
  * IBindingListView (e.g. _BindingSource_) 





There are six types of built-in column types :






  
  * DataGridViewTextBoxColumn 


  
  * DataGridViewCheckBoxColumn 


  
  * DataGridViewImageColumn (_Image_ and _Icon_) 


  
  * DataGridViewButtonColumn 


  
  * DataGridViewComboBoxColumn 


  
  * DataGridViewLinkColumn 


  
  * Custom : _DataGridViewColumn_





The _DataGridView_ will automatically choose the first three column types for binding to strings, numerics, booleans, Image and Icon.





The _CellValidating_ event is raised and provides _ColumnIndex_ and _Cancel_ properties on the _EventArgs_.





Need to summarise styles including custom painting.





## Chapter 9 : Working with XML





### Lesson 1 : Reading and Writing XML with the XmlReader and XmlWriter Classes





#### The XmlReader Class





Provides rapid, noncached, forward-only access to XML documents. The _XmlReader_ abstract class has properties :




    
    <span style="color:blue;">class </span><span style="color:#2b91af;">XmlReaderWriterExamples
    </span>{
        <span style="color:blue;">public void </span>XmlReaderExample()
        {
            <span style="color:green;">// A schema set provides a memory cache of schemas for performance benefits
            </span><span style="color:#2b91af;">XmlSchemaSet </span>xmlschemaset = <span style="color:blue;">new </span><span style="color:#2b91af;">XmlSchemaSet</span>();
            xmlschemaset.Add(<span style="color:#a31515;">"urn:my-schema"</span>, <span style="color:#a31515;">"myschema.xsd"</span>);
    
            <span style="color:#2b91af;">XmlReaderSettings </span>settings = <span style="color:blue;">new </span><span style="color:#2b91af;">XmlReaderSettings</span>();
            settings.Schemas.Add(<span style="color:#a31515;">"urn:my-schema"</span>, <span style="color:#a31515;">"myschema.xsd"</span>);   <span style="color:green;">// targetNameSpace, schemaUri
            </span>settings.Schemas = xmlschemaset;    <span style="color:green;">// the alternative if using a schema set
            </span>settings.ValidationType = <span style="color:#2b91af;">ValidationType</span>.Schema; <span style="color:green;">// or Auto, None, DTD, XDR
            </span>settings.ConformanceLevel = <span style="color:#2b91af;">ConformanceLevel</span>.Auto; <span style="color:green;">// or Document, Fragment
            </span>settings.IgnoreWhitespace = <span style="color:blue;">true</span>;
            settings.IgnoreComments = <span style="color:blue;">true</span>;
    
            <span style="color:green;">// If you're validating against a DTD then Schemas isn't set but...
            </span>settings.ProhibitDtd = <span style="color:blue;">false</span>;   <span style="color:green;">// it needs to be enabled first
            </span>settings.ValidationType = <span style="color:#2b91af;">ValidationType</span>.DTD;
    
            <span style="color:green;">// If there's a schema defined in-line in the XML then set the flag..
            </span>settings.ValidationFlags |= <span style="color:#2b91af;">XmlSchemaValidationFlags</span>.ProcessInlineSchema;
    
            <span style="color:green;">// Hook up the validation event handling
            </span>settings.ValidationFlags |= <span style="color:#2b91af;">XmlSchemaValidationFlags</span>.ReportValidationWarnings;
            settings.ValidationEventHandler += <span style="color:blue;">new </span><span style="color:#2b91af;">ValidationEventHandler</span>(settings_ValidationEventHandler);
    
            <span style="color:green;">// Call the static factory method Create() which accepts a Stream, TextReader, XmlReader or Uri
            </span><span style="color:blue;">using </span>(<span style="color:#2b91af;">XmlReader </span>xmlreader = <span style="color:#2b91af;">XmlReader</span>.Create(<span style="color:#a31515;">"books.xml"</span>, settings))
            {
                <span style="color:green;">// Call methods in a prescribed manner
                </span>xmlreader.Read();
                xmlreader.ReadStartElement(<span style="color:#a31515;">"book"</span>);
                xmlreader.ReadStartElement(<span style="color:#a31515;">"title"</span>);
                <span style="color:#2b91af;">Console</span>.Write(<span style="color:#a31515;">"The content of the title element:  "</span>);
                <span style="color:#2b91af;">Console</span>.WriteLine(xmlreader.ReadString());
                xmlreader.ReadEndElement();
                xmlreader.ReadStartElement(<span style="color:#a31515;">"price"</span>);
                <span style="color:#2b91af;">Console</span>.Write(<span style="color:#a31515;">"The content of the price element:  "</span>);
                <span style="color:#2b91af;">Console</span>.WriteLine(xmlreader.ReadString());
                xmlreader.ReadEndElement();
                xmlreader.ReadEndElement();
    
                <span style="color:green;">// or, read items and determine the contents on the fly
                </span><span style="color:blue;">while </span>(xmlreader.Read() && !xmlreader.EOF) <span style="color:green;">//don't strictly need EOF check but just for example
                </span>{
                    <span style="color:blue;">if </span>(xmlreader.IsStartElement())
                    {
                        <span style="color:blue;">if </span>(xmlreader.IsEmptyElement)
                            <span style="color:#2b91af;">Console</span>.WriteLine(<span style="color:#a31515;">"<{0}/>"</span>, xmlreader.Name);
                        <span style="color:blue;">else
                        </span>{
                            <span style="color:#2b91af;">Console</span>.Write(<span style="color:#a31515;">"<{0}> "</span>, xmlreader.Name);
                            xmlreader.Read(); <span style="color:green;">// Read the start tag.
                            </span><span style="color:blue;">if </span>(xmlreader.IsStartElement())  <span style="color:green;">// Handle nested elements.
                                </span><span style="color:#2b91af;">Console</span>.Write(<span style="color:#a31515;">"\r\n<{0}>"</span>, xmlreader.Name);
                            <span style="color:#2b91af;">Console</span>.WriteLine(xmlreader.ReadString());  <span style="color:green;">//Read the text content of the element.
                        </span>}
                    }
                }
    
                <span style="color:green;">// Skips to the next content node or end-of-file if not already a content node
                // Content is : CDATA, Element, EndElement, EntityReference, EndEntity or nonwhite space text
                </span><span style="color:#2b91af;">XmlNodeType </span>contentNode = xmlreader.MoveToContent(); 
    
                xmlreader.Skip(); <span style="color:green;">// Skips children of current node
     
                // Attributes can also be read and when they are, the .Name, .Value properties etc.
                // reflect the attribute instead of the element until repositioned
                </span><span style="color:blue;">if </span>(xmlreader.HasAttributes)
                {
                    <span style="color:#2b91af;">Console</span>.WriteLine(<span style="color:#a31515;">"Attributes of <" </span>+ xmlreader.Name + <span style="color:#a31515;">">"</span>);
                    xmlreader.MoveToFirstAttribute(); <span style="color:green;">// not needed here but an example of the method
                    </span><span style="color:blue;">while </span>(xmlreader.MoveToNextAttribute())
                    {
                        <span style="color:blue;">if </span>(xmlreader.HasValue)
                        {
                            <span style="color:#2b91af;">Console</span>.WriteLine(<span style="color:#a31515;">" {0}={1}"</span>, xmlreader.Name, xmlreader.Value);
                        }
                    }
                    <span style="color:green;">// Move the reader back to the element node.
                    </span>xmlreader.MoveToElement();
                }
    
                <span style="color:blue;">if </span>(xmlreader.AttributeCount > 0)
                {
                    <span style="color:#2b91af;">Console</span>.WriteLine(<span style="color:#a31515;">"The first attribute value : {0}"</span>, xmlreader.GetAttribute(0));
                    <span style="color:#2b91af;">Console</span>.WriteLine(<span style="color:#a31515;">"A named attribute value : {0}"</span>, xmlreader.GetAttribute(<span style="color:#a31515;">"aName"</span>));
                }
    
                <span style="color:blue;">if </span>(xmlreader.NodeType == <span style="color:#2b91af;">XmlNodeType</span>.Element)
                {
                    <span style="color:green;">// concatenates all text, significant white space and CDATA until the next markup
                    </span>xmlreader.ReadString();
    
                    <span style="color:green;">// returns all content of the current node including markup (but not the node itself)
                    </span>xmlreader.ReadInnerXml();
    
                    <span style="color:green;">// As Inner but includes the current node
                    </span>xmlreader.ReadOuterXml();
    
                    <span style="color:green;">// The following (for other types too) allow typed data to be retrieved
                    </span>xmlreader.ReadContentAsBoolean();
                    xmlreader.ReadElementContentAsBoolean();
                }
    
            }
        }
    
        <span style="color:blue;">void </span>settings_ValidationEventHandler(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">ValidationEventArgs </span>e)
        {
            <span style="color:#2b91af;">Console</span>.WriteLine(<span style="color:#a31515;">"Validation Error : {0} of severity {1}"</span>, e.Message, e.Severity);
        }
    }


[](http://11011.net/software/vspaste)



#### The XmlTextReader : XmlReader Class





The _XmlTextReader_ is an implementation of _XmlReader_ and it’s constructor can accept a _Stream, TextReader _or a Url including filenames. Microsoft recommends calling _XmlReader.Create() _instead of using _XmlTextReader_.













#### The XmlNodeReader : XmlReader Class





The _XmlNodeReader _is an implementation of _XmlReader_ that can be used to read a DOM as a stream of XML. However, the recommended best practice is to use and _XmlReader.Create()_ and _XmlReaderSettings_ to take full advantage of all the .Net new features.




    
    <span style="color:#2b91af;">XmlDocument </span>doc = <span style="color:blue;">new </span><span style="color:#2b91af;">XmlDocument</span>();
    doc.Load(<span style="color:#a31515;">"MyXml.xml"</span>);
    <span style="color:#2b91af;">XmlNodeReader </span>nodereader = <span style="color:blue;">new </span><span style="color:#2b91af;">XmlNodeReader</span>(doc);
    <span style="color:blue;">while </span>(nodereader.Read())
    {
        <span style="color:green;">// Read the XmlDocument as a stream of XML.
    </span>}





[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)









#### The XmlValidatingReader : XmlReader Class





The _XmlValidatingReader_ is obsolete. It wraps an existing _XmlReader_ via a constructor, and will detect and check inline and referenced schemas. It provides _ValidationType_ property and _ValidationEvent_ event.





#### The XmlWriter Class




    
    <span style="color:blue;">using </span>System;
    <span style="color:blue;">using </span>System.Xml;
    <span style="color:blue;">using </span>System.IO;
    <span style="color:blue;">using </span>System.Xml.Schema;
    <span style="color:blue;">using </span>System.Text;
    
    <span style="color:blue;">class </span><span style="color:#2b91af;">XmlReaderWriterExamples
    </span>{
        <span style="color:blue;">public void </span>XmlWriterExample()
        {
            <span style="color:#2b91af;">XmlReader </span>xmlreader;
    
            <span style="color:#2b91af;">XmlWriterSettings </span>settings = <span style="color:blue;">new </span><span style="color:#2b91af;">XmlWriterSettings</span>();
            settings.CheckCharacters = <span style="color:blue;">true</span>;    <span style="color:green;">// Raises XmlException if any character is an invalid XML character
            </span>settings.ConformanceLevel = <span style="color:#2b91af;">ConformanceLevel</span>.Auto; <span style="color:green;">// or Document, Fragment
            </span>settings.Encoding = <span style="color:#2b91af;">Encoding</span>.UTF8;
            settings.Indent = <span style="color:blue;">true</span>;
            settings.IndentChars = (<span style="color:#a31515;">"    "</span>);
            settings.NewLineChars = <span style="color:#a31515;">"\r\n"</span>;
            settings.NewLineOnAttributes = <span style="color:blue;">false</span>;
            settings.OmitXmlDeclaration = <span style="color:blue;">false</span>;
    
            <span style="color:blue;">using </span>(<span style="color:#2b91af;">XmlWriter </span>xmlwriter = <span style="color:#2b91af;">XmlWriter</span>.Create(<span style="color:#a31515;">"books.xml"</span>, settings))
            {
                xmlwriter.WriteComment(<span style="color:#a31515;">"This is just a comment for our test file"</span>);
    
                <span style="color:green;">// Write XML data.
                </span>xmlwriter.WriteStartElement(<span style="color:#a31515;">"book"</span>);
    
                <span style="color:green;">// write namespace manually. Subsequent children will have a x: prefix
                </span>xmlwriter.WriteAttributeString(<span style="color:#a31515;">"xmlns"</span>, <span style="color:#a31515;">"x"</span>, <span style="color:blue;">null</span>, <span style="color:#a31515;">"urn:1"</span>);   <span style="color:green;">// writes xmlns:x="urn:1" to the <Book> element
    
                // An alternative method for writing attributes which makes use of WriteValue() that
                // offers several typed methods for CLR types which are converted using XmlConvert
                </span>xmlwriter.WriteStartAttribute(<span style="color:#a31515;">"available"</span>);
                xmlwriter.WriteValue(<span style="color:blue;">true</span>);
                xmlwriter.WriteEndAttribute();
    
                <span style="color:green;">// Two methods that copy all attributes and elements from an XmlReader
                </span>xmlwriter.WriteAttributes(xmlreader, <span style="color:blue;">true</span>);
                xmlwriter.WriteNode(xmlreader, <span style="color:blue;">true</span>);
    
                xmlwriter.WriteElementString(<span style="color:#a31515;">"price"</span>, <span style="color:#a31515;">"19.95"</span>);                <span style="color:green;">// writes <x:price>19.95</price>
    
                </span>xmlwriter.WriteEndElement();                                   <span style="color:green;">// writes </Book> use WriteFullEndElement() 
                </span>xmlwriter.Flush();
            }
        }
    }





### [](http://11011.net/software/vspaste)Lesson 2 : Managing XML with the XML Document Object Model





The following diagram highlights the Xml DOM object model with the equivalent W3C class names in brackets.





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb20.png)](http://philiphendry.files.wordpress.com/2008/07/image20.png)
















    
    <span style="color:blue;">using </span>System;
    <span style="color:blue;">using </span>System.Xml;
    <span style="color:blue;">using </span>System.IO;
    <span style="color:blue;">using </span>System.Xml.Schema;
    <span style="color:blue;">using </span>System.Text;
    
    <span style="color:blue;">class </span><span style="color:#2b91af;">XmlDocumentExamples
    </span>{
    
        <span style="color:blue;">public void </span>XmlDocumentExample()
        {
            <span style="color:#2b91af;">XmlReaderSettings </span>xmlreadersettings = <span style="color:blue;">new </span><span style="color:#2b91af;">XmlReaderSettings</span>();
            xmlreadersettings.ValidationEventHandler += <span style="color:blue;">new </span><span style="color:#2b91af;">ValidationEventHandler</span>(xmlreadersettings_ValidationEventHandler);
    
            <span style="color:#2b91af;">XmlReader </span>xmlreader = <span style="color:#2b91af;">XmlReader</span>.Create(<span style="color:#a31515;">"myinputuri"</span>, xmlreadersettings);
    
            <span style="color:#2b91af;">XmlDocument </span>xmldocument = <span style="color:blue;">new </span><span style="color:#2b91af;">XmlDocument</span>();
            xmldocument.PreserveWhitespace = <span style="color:blue;">true</span>;
            xmldocument.LoadXml(<span style="color:#a31515;">"<?xml version='1.0'?><books></books>"</span>);
            <span style="color:green;">// Alternatively load the data from a Stream, filename, TextReader 
            // or derivative of XmlReader. To validate XML against a schema 
            // during loading a XmlReader must be used in conjunction with 
            // a XmlReaderSettings.
            </span>xmldocument.Load(xmlreader);
    
            <span style="color:green;">// To validate any changes made to the DOM call Validate() and handle
            // and event callbacks. Can also optionally base the node to validate
            // rather than the whole document. 
            </span>xmldocument.Schemas = xmlreadersettings.Schemas;
            xmldocument.Validate(xmlreadersettings_ValidationEventHandler);
    
            <span style="color:green;">// Accessing the root element and creating attributes
            </span><span style="color:#2b91af;">XmlElement </span>root = xmldocument.DocumentElement;
            root.SetAttribute(<span style="color:#a31515;">"modified"</span>, <span style="color:#2b91af;">DateTime</span>.Now.ToString());
    
            <span style="color:green;">// Creating a new element. Also CreateComment, CreateCDataSection, CreateDocumentFragment
            // CreateDocumentType, CreateElement, CreateProcessinInstruction, CreateTextNode,
            // CreateXmlDeclaration, CreateWhitespace, CreateSignificantWhitespace.
            </span><span style="color:#2b91af;">XmlNode </span>newnode1 = xmldocument.CreateElement(<span style="color:#a31515;">"book"</span>);
            root.AppendChild(newnode1);
    
            <span style="color:green;">// Creating a comment and inserting before
            </span><span style="color:#2b91af;">XmlComment </span>newcomment = xmldocument.CreateComment(<span style="color:#a31515;">"The first book"</span>);
            root.InsertBefore(newcomment, newnode1); <span style="color:green;">// Insert newnode2 before newnode1
    
            // Cloning a node, adding an attribute and prepending
            </span><span style="color:#2b91af;">XmlElement </span>newnode3 = (<span style="color:#2b91af;">XmlElement</span>)newnode1.Clone();
            newnode3.SetAttribute(<span style="color:#a31515;">"title"</span>, <span style="color:#a31515;">"My First Book"</span>);
            root.PrependChild(newnode3); <span style="color:green;">// Insert at the beginning of the child list
    
            // Removal examples for attributes and elements
            </span>root.RemoveAttributeNode(<span style="color:#a31515;">"modified"</span>, <span style="color:#a31515;">""</span>);
            root.RemoveAllAttributes();
            root.Attributes.RemoveAll();
            root.RemoveAll();
            root.RemoveChild(newnode1);
    
            <span style="color:green;">// For any node inheriting from XmlCharacterData, text can be replaced
            </span>newcomment.ReplaceData(4, 5, <span style="color:#a31515;">"1st"</span>);
    
            <span style="color:green;">// Searching
            </span><span style="color:#2b91af;">XmlNodeList </span>nodelist = xmldocument.DocumentElement.FirstChild.SelectNodes(<span style="color:#a31515;">@"//book"</span>);
            <span style="color:#2b91af;">XmlNode </span>node = xmldocument.DocumentElement.SelectSingleNode(<span style="color:#a31515;">"@//book[0]"</span>);
    
            <span style="color:green;">// Saving
            </span>xmldocument.Save(<span style="color:#a31515;">@"c:\temp\example.xml"</span>); <span style="color:green;">//or a Stream, TextWriter or XmlWriter
        </span>}
    
        <span style="color:blue;">void </span>xmlreadersettings_ValidationEventHandler(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">ValidationEventArgs </span>e)
        {
            <span style="color:blue;">throw new </span><span style="color:#2b91af;">NotImplementedException</span>();
        }
    }


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)



#### The XmlNamedNodeMap









_XmlNamedNodeMap_ is a non-hierarchical list of _XmlNode_ available from the following properties :






  
  * XmlElement.Attributes 


  
  * XmlDocumentType.Entities 


  
  * XmlDocumentType.Notations 





and provides the following methods :






  
  * GetNamedItem(string name) 


  
  * Item(int index) 


  
  * RemoveNamedItem(string name) 


  
  * SetNamedItem(XmlNode node) 





#### The XmlNodeList





_XmlNodeList_ is an ordered list of _XmlNode_ available from the following properties :






  
  * XmlNode.ChildNodes 


  
  * XmlDocument.GetElementsByTagName 


  
  * XmlElement.GetElementsByTagName 


  
  * XmlNode.SelectNodes 





and provides _Count, Item_ and enumerator methods.





#### DOM Events






  
  * NodeInserting 


  
  * NodeInserted 


  
  * NodeRemoving 


  
  * NodeRemoved 


  
  * NodeChanging 


  
  * NodeChanged 





Each event delegate is passed a _XmlNodeChangedEventArgs_ which has properties :






  
  * Action (XmlNodeChangedAction.Insert, Remove, Change) 


  
  * NewParent 


  
  * NewValue 


  
  * Node 


  
  * OldParent 


  
  * OldValue 





## Chapter 10 : Printing in Windows Forms





### Lesson 1 : Managing the Print Process by Using Print Dialogs





#### The PrintDocument Class





Typically create a _PrintDocument _component, set properties that describe what to print using _PrinterSettings_ and _PageSettings_ then call the _Print_ method. The _PrintDocument_ can be passed to a number of other components to allow the user to change settings, see a preview and ultimately print the document.





#### The PrintDialog Component





_PrintDialog_ presents an interface to the user including a list of printers, number of copies and selection of page ranges to print. It has properties :






  
  * AllowCurrentPage 


  
  * AllowPrintToFile 


  
  * AllowSelection 


  
  * AllowSomePages 


  
  * Document (a _PrintDocument_ associated with the dialog component) 


  
  * PrinterSettings (these settings will be modified according to user selection in the print dialog) 


  
  * PrintToFile (whether the print to file checkbox is selected) 


  
  * ShowHelp (the help button is visible) 


  
  * ShowNetwork (the network button is visible) 





The dialog results can be queried :





<blockquote>
  
> 
> DialogResult result = PrintDialog1.ShowDialog() 
      
if (result == DialogResult.OK)
> 
> 
</blockquote>





#### The PageSetupDialog Component





_PageSetupDialog_ allows the user to change the size of the paper, source, orientation and margins. Again changes are automatically reflected in the _PrinterSettings. _Properties :






  
  * AllowMargins 


  
  * AllowOrientation 


  
  * AllowPaper 


  
  * AllowPrinter (A Printer… button) 


  
  * Document (a _PrintDocument_) 


  
  * EnableMetric (automatic conversion to/from hundredths of an inch) 


  
  * MinMargins (minimum margins in hundredths of an inch) 


  
  * ShowHelp 


  
  * ShowNetwork 









The preferred method to display is :





<blockquote>
  
> 
> pageSetupDialog1.Document = printDocument1; 
      
pageSetupDialog1.ShowDialog();
> 
> 
</blockquote>





#### The PrintPreviewDialog Component





<blockquote>
  
> 
> printPreviewDialog1.Document = printDocument1; 
      
printPreviewDialog1.ShowDialog();
> 
> 
</blockquote>





### Lesson 2 : Constructing Print Document





#### The PrintDocument Component





When _PrintDocument.Print()_ is called _PrintPage_ events are raised which must create the page to be printed and track which page is to be printed now/next. The _PrintPage_ event is passed a _PrintPageEventArgs_ which has properties :






  
  * Cancel 


  
  * Graphics 


  
  * HasMorePages (handler must set to true to indicate more pages are still to be printed) 


  
  * MarginBounds (a _Rectangle_ representing the area within the margins) 


  
  * PageBounds (a _Retangle_ representing the total page area – used to calculate where out-of-margin printing goes e.g. headers, footers) 


  
  * PageSettings (gets or sets the _PageSettings _object for the current page) 




    
    <span style="color:blue;">using </span>System;
    <span style="color:blue;">using </span>System.Collections.Generic;
    <span style="color:blue;">using </span>System.ComponentModel;
    <span style="color:blue;">using </span>System.Data;
    <span style="color:blue;">using </span>System.Drawing;
    <span style="color:blue;">using </span>System.Linq;
    <span style="color:blue;">using </span>System.Text;
    <span style="color:blue;">using </span>System.Windows.Forms;
    <span style="color:blue;">using </span>System.Diagnostics;
    <span style="color:blue;">using </span>System.Drawing.Drawing2D;
    
    <span style="color:blue;">namespace </span>WindowsBasedClientDevelopment
    {
        <span style="color:blue;">public partial class </span><span style="color:#2b91af;">Form1 </span>: <span style="color:#2b91af;">Form
        </span>{
            <span style="color:blue;">public </span>Form1()
            {
                InitializeComponent();
            }
    
            <span style="color:blue;">private void </span>Form1_Load(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
            {
                printDocument1.PrintPage += <span style="color:blue;">new </span>System.Drawing.Printing.<span style="color:#2b91af;">PrintPageEventHandler</span>(printDocument1_PrintPage);
                printDocument1.EndPrint += <span style="color:blue;">new </span>System.Drawing.Printing.<span style="color:#2b91af;">PrintEventHandler</span>(printDocument1_EndPrint);
            }
    
            <span style="color:blue;">void </span>printDocument1_EndPrint(<span style="color:blue;">object </span>sender, System.Drawing.Printing.<span style="color:#2b91af;">PrintEventArgs </span>e)
            {
                <span style="color:#2b91af;">MessageBox</span>.Show(<span style="color:#a31515;">"Printing has finished"</span>);
            }
    
            <span style="color:blue;">private void </span>btnPrint_Click(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
            {
                printDocument1.Print();
            }
    
            <span style="color:blue;">private void </span>btnPreview_Click(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
            {
                printPreviewDialog1.Document = printDocument1;
                printPreviewDialog1.ShowDialog();
            }
    
            <span style="color:blue;">void </span>printDocument1_PrintPage(<span style="color:blue;">object </span>sender, System.Drawing.Printing.<span style="color:#2b91af;">PrintPageEventArgs </span>e)
            {
                e.Graphics.DrawEllipse(<span style="color:#2b91af;">Pens</span>.Black, e.MarginBounds);
    
                <span style="color:#2b91af;">GraphicsPath </span>path = <span style="color:blue;">new </span><span style="color:#2b91af;">GraphicsPath</span>();
                path.AddPolygon(<span style="color:blue;">new </span><span style="color:#2b91af;">Point</span>[] { <span style="color:blue;">new </span><span style="color:#2b91af;">Point</span>(1, 1), <span style="color:blue;">new </span><span style="color:#2b91af;">Point</span>(12, 55), <span style="color:blue;">new </span><span style="color:#2b91af;">Point</span>(34, 8) });
                path.AddRectangle(<span style="color:blue;">new </span><span style="color:#2b91af;">Rectangle</span>(33, 43, 20, 20));
                e.Graphics.DrawPath(<span style="color:#2b91af;">Pens</span>.Red, path);
    
                <span style="color:#2b91af;">Font </span>font = <span style="color:blue;">new </span><span style="color:#2b91af;">Font</span>(<span style="color:#a31515;">"Tahoma"</span>, 12, <span style="color:#2b91af;">FontStyle</span>.Regular, <span style="color:#2b91af;">GraphicsUnit</span>.Pixel);
                <span style="color:blue;">float </span>fontHeight = font.GetHeight(e.Graphics);
                e.Graphics.DrawString(<span style="color:#a31515;">"Hello world!"</span>, font, <span style="color:#2b91af;">Brushes</span>.Firebrick, 200, 200);
    
                e.HasMorePages = <span style="color:blue;">false</span>;
            }
        }
    }


[](http://11011.net/software/vspaste)



#### Security and Printing





Permission to print is defined by the _PrintingPermission_ class with which can be specified four _PrintingPermissionLevel_ values :






  
  * AllPrinting (unrestricted access) 


  
  * DefaultPrinting (programmatic printing to the default printer and other printers via the printer dialog box – granted to applications running in _intranet_ or _internet_ security zones by default) 


  
  * SafePrinting (only allows printing via the printer dialog box) 


  
  * NoPrinting (no access to the printers) 













### Lesson 3 : Creating a Customized PrintPreview Component





#### The PrintPreviewControl Control





Properties :






  
  * AutoZoom (whether resizing the control or changing number of displayed pages automatically changes zoom) 


  
  * Columns (number of pages displayed horizontally) 


  
  * Document (the _PrintDocument_) 


  
  * Rows (number of pages displayed vertically) 


  
  * StartPage (the page of the _PrintDocument_ to be displayed in the first page of the control) 


  
  * UseAntiAlias 


  
  * Zoom (1.0 is full size, 5.0 is 500% of full size, 0.25 is 25% ) 





_PrintPreviewControl_ can be subclassed to add custom properties and methods.





## Chapter 11 : Advanced Topics in Windows Forms





### Lesson 1 : Implementing Drag-and-Drop Functionality





Source control events :






  
  * MouseDown (usually _DoDragDrop_ method is called) 


  
  * GiveFeedBack (can set a custom mouse pointer) 


  
  * QueryContinueDrag (can determine whether the drag event should be cancelled) 





Target control events :






  
  * DragEnter 


  
  * DragOver 


  
  * DragDrop 


  
  * DragLeave 





Usual operation :






  
  1. _DoDragDrop _method is called on the source control – usually in _MouseDown_. The method copies desired data from source to a new instance of _DataObject._


  
  2. _GiveFeedBack_ and _QueryContinueDrag_ events are raised. 


  
  3. Mouse pointer is dragged over a target (any control with _AllowDrop = True._) 


  
  4. Mouse button is released over a target and _DragDrop_ is raised. 




    
    <span style="color:blue;">using </span>System;
    <span style="color:blue;">using </span>System.Collections.Generic;
    <span style="color:blue;">using </span>System.ComponentModel;
    <span style="color:blue;">using </span>System.Data;
    <span style="color:blue;">using </span>System.Drawing;
    <span style="color:blue;">using </span>System.Linq;
    <span style="color:blue;">using </span>System.Text;
    <span style="color:blue;">using </span>System.Windows.Forms;
    <span style="color:blue;">using </span>System.Diagnostics;
    <span style="color:blue;">using </span>System.Drawing.Drawing2D;
    
    <span style="color:blue;">namespace </span>WindowsBasedClientDevelopment
    {
        <span style="color:blue;">public partial class </span><span style="color:#2b91af;">Form1 </span>: <span style="color:#2b91af;">Form
        </span>{
            <span style="color:blue;">public </span>Form1()
            {
                InitializeComponent();
            }
    
            <span style="color:blue;">private void </span>Form1_Load(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
            {
                <span style="color:#2b91af;">Button </span>source = <span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>();
                source.Text = <span style="color:#a31515;">"Source"</span>;
                source.Location = <span style="color:blue;">new </span><span style="color:#2b91af;">Point</span>(10, 10);
                source.MouseDown += <span style="color:blue;">new </span><span style="color:#2b91af;">MouseEventHandler</span>(source_MouseDown);
    
                <span style="color:#2b91af;">Button </span>target = <span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>();
                target.Text = <span style="color:#a31515;">"Target"</span>;
                target.Location = <span style="color:blue;">new </span><span style="color:#2b91af;">Point</span>(50, 50);
                target.AllowDrop = <span style="color:blue;">true</span>;
                target.DragEnter += <span style="color:blue;">new </span><span style="color:#2b91af;">DragEventHandler</span>(target_DragEnter);
                target.DragDrop += <span style="color:blue;">new </span><span style="color:#2b91af;">DragEventHandler</span>(target_DragDrop);
    
                <span style="color:blue;">this</span>.Controls.Add(source);
                <span style="color:blue;">this</span>.Controls.Add(target);
            }
    
            <span style="color:blue;">void </span>target_DragDrop(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">DragEventArgs </span>e)
            {
                <span style="color:#2b91af;">IDataObject </span>data = e.Data;
                <span style="color:#2b91af;">MessageBox</span>.Show(<span style="color:#2b91af;">String</span>.Format(<span style="color:#a31515;">"Accepted data '{0}'"</span>, data.GetData(<span style="color:#2b91af;">DataFormats</span>.StringFormat)));
            }
    
            <span style="color:blue;">void </span>target_DragEnter(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">DragEventArgs </span>e)
            {
                <span style="color:green;">// Check the data is in the format we accept and that the allowed effects
                // contains the effect we accept
                </span><span style="color:blue;">if </span>(e.Data.GetDataPresent(<span style="color:#2b91af;">DataFormats</span>.StringFormat) && (e.AllowedEffect & <span style="color:#2b91af;">DragDropEffects</span>.Copy) > 0)
                {
                    e.Effect = <span style="color:#2b91af;">DragDropEffects</span>.Copy;
                }
            }
    
            <span style="color:blue;">void </span>source_MouseDown(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">MouseEventArgs </span>e)
            {
                <span style="color:blue;">object </span>objectdata = <span style="color:#a31515;">"Just some text"</span>;
                <span style="color:#2b91af;">Button </span>source = (<span style="color:#2b91af;">Button</span>)sender;
    
                <span style="color:green;">// Set the data and allowed effects
                </span>source.DoDragDrop(objectdata, <span style="color:#2b91af;">DragDropEffects</span>.Copy | <span style="color:#2b91af;">DragDropEffects</span>.Move);
            }
        }
    }


[](http://11011.net/software/vspaste)







#### Drag-and-Drop in a TreeView






  
  1. Set _AllowDrop = True_ on the treeview which will enable the _DragEnter_ and _DragDrop_ events. 


  
  2. In _TreeView.ItemDrag_ event handler call _DoDragDrop_ and specify the _ItemDragEventArgs.Item_ as the data property. 


  
  3. In _TreeView.DragEnter_ set the _DragDropEventArgs.Effect_ property appropriately. 


  
  4. In _TreeView.DragDrop_ examine _DragDropEventArgs.DataObject_ to determine if a _TreeNode_ is present. 





### Leson 2 : Implementing Globalization and Localization for a Windows Forms Application






  
  * Globalization (formatting existing data for the current culture) 


  
  * Localization (retrieving appropriate data for the current culture) 





#### Culture






  
  * Neutral cultures (only specify the language code e.g. en) 


  
  * Specific cultures (specify the language and region codes e.g. en-CA) 





Most codes are in the format <language>-<region> but there are some exceptions.





To change the current culture :




    
    <span style="color:green;">// Change the culture used for formatting data
    </span><span style="color:#2b91af;">Thread</span>.CurrentThread.CurrentCulture = <span style="color:blue;">new </span>System.Globalization.<span style="color:#2b91af;">CultureInfo</span>(<span style="color:#a31515;">"fr-CA"</span>);
    <span style="color:#2b91af;">Console</span>.WriteLine((500).ToString(<span style="color:#a31515;">"C"</span>)); <span style="color:green;">// Formatted currency using the current culture
    
    // Change the culture used for displaying user interfaces
    </span><span style="color:#2b91af;">Thread</span>.CurrentThread.CurrentUICulture = <span style="color:blue;">new </span>System.Globalization.<span style="color:#2b91af;">CultureInfo</span>(<span style="color:#a31515;">"fr-CA"</span>);


[](http://11011.net/software/vspaste)







The UI culture must be set in a Form’s constructor or in the application Main method.









Every form has a _Localizable_ property which, if true, will inform VS to automatically handles creation of appropriate resource files according to the _CurrentUICulture_. The _Language_ property is available at design time only and allows the current form to be modified for each particular language – as soon as a property is modified, VS creates the necessary language resource file in the project.





#### Implementing Right-to-Left Display









The Control type provides a property _RightToLeft_ which can be set to _Yes, No_, or _Inherit_ (from it’s parent.) When set to yes, text will be right-aligned, scrollbars appears on the left, horizontal scrollbars are initialized on the right side, _CheckBox.CheckAlign_ is reveresed, _TabControl_ is reversed etc. 









The _RightToLeftLayout_ property will completely mirror a form.





### Lesson 3 : Implementing MDI Forms





_IsMdiContainer_ property is set on the parent form and each child has it’s _MdiParent_ property set to the parent form before _Show()_ is called. The active child form is given by the parent._ActiveMDIChild_ property – it returns _null_ if there is no parent. _ActiveControl_ could be used on the active mdi child to implement cut & paste functionality for example.





The _LayoutMdi()_ method can be called on the parent with one of the following from the _MdiLayout_ enum :






  
  * ArrangeIcons 


  
  * Cascade 


  
  * TileHorizontal 


  
  * TileVertical 





The following code illustrates creating a parent, child and a menu that automatically lists the current child windows :




    
    <span style="color:#2b91af;">MenuStrip </span>menu = <span style="color:blue;">new </span><span style="color:#2b91af;">MenuStrip</span>();
    <span style="color:#2b91af;">ToolStripMenuItem </span>toolstrip = <span style="color:blue;">new </span><span style="color:#2b91af;">ToolStripMenuItem</span>(<span style="color:#a31515;">"Windows"</span>);
    menu.Items.Add(toolstrip);
    menu.MdiWindowListItem = toolstrip;
    
    <span style="color:blue;">this</span>.IsMdiContainer = <span style="color:blue;">true</span>;
    <span style="color:blue;">this</span>.Controls.Add(menu);
    
    <span style="color:#2b91af;">Form2 </span>form2 = <span style="color:blue;">new </span><span style="color:#2b91af;">Form2</span>();
    form2.MdiParent = <span style="color:blue;">this</span>;
    form2.Show();





## [](http://11011.net/software/vspaste)Chapter 12 : Enhancing Usability





### Lesson 1 : Implementing Accessibility





#### Support Standard System Settings





There are a number of classes in the _System.Drawing_ namespace that expose system settings monitoring the system for any changes to the settings :






  
  * SystemBrushes 


  
  * SystemColors 


  
  * SystemFonts 


  
  * SystemIcons 


  
  * SystemPens 





Accessibility properties of Windows Forms Controls :






  
  * AccessibleDescription (reported to accessibility aids) 


  
  * AccessibleName (reported to accessibility aids) 


  
  * AccessibleRole (reported to accessibilty aids. A member of the _AccessibleRole_ enumeration determining the UI element type such as TitleBar, MenuBar, Scrollbar, Row, Cell, Text) 


  
  * AccessibilityObject (a read-only instance of _AccessibleObject _providing information about the control to usability aids.) 


  
  * AccessibleDefaultActionDescription (description of the default action of a control.) 





### Lesson 2 : Using User Assistance Controls and Components





#### The PropertyGrid Control

























_PropertyGrid_ provides a VS style property grid which displays and allows changes to the properties of a control that is currently selected by setting the _SelectedObject_ property. _PropertySort_ can be set to _PropertySort.Alphabetical, Categorized, CategorizedAlphabetical_ or _NoSort_.





#### The ProgressBar Control





Properties :






  
  * Mximum 


  
  * Minimum 


  
  * Step 


  
  * Value 





Methods :






  
  * PerformStep() (advances _Value_ by _Step_ amount) 


  
  * Increment(value) 





#### The StatusStrip : ToolStrip Control




    
    <span style="color:#2b91af;">StatusStrip </span>status = <span style="color:blue;">new </span><span style="color:#2b91af;">StatusStrip</span>();
    status.Dock = <span style="color:#2b91af;">DockStyle</span>.Bottom;
    
    <span style="color:#2b91af;">ToolStripStatusLabel </span>label = <span style="color:blue;">new </span><span style="color:#2b91af;">ToolStripStatusLabel</span>(<span style="color:#a31515;">"Some status text"</span>);
    status.Items.Add(label);
    
    <span style="color:#2b91af;">ToolStripProgressBar </span>progress = <span style="color:blue;">new </span><span style="color:#2b91af;">ToolStripProgressBar</span>(<span style="color:#a31515;">"progress"</span>);
    progress.Minimum = 0;
    progress.Maximum = 100;
    progress.Step = 10;
    progress.Value = 40;
    status.Items.Add(progress);
    
    <span style="color:blue;">this</span>.Controls.Add(status);





#### [](http://11011.net/software/vspaste)The ToolTip Component





Properties :






  
  * Active 


  
  * AutomaticDelay (if AutomaticDelay = N then sets AutoPopDelay = 5 * N, InitialDelay = N and ReshowDelay = N / 5 in one go) 


  
  * AutoPopDelay (display timeout) 


  
  * InitialDelay (before display) 


  
  * IsBalloon 


  
  * ReshowDelay 


  
  * ShowAlways (displays whether parent control is inactive) 


  
  * ToolTipIcon 


  
  * ToolTipTitle 


  
  * UseAnimation 


  
  * UseFading 





Methods :






  
  * GetToolTip 


  
  * SetToolTip 





Automatic display of tool tips require :






  
  * _ToolTip.Active_ = true 


  
  * _ToolTip_ must be set for a control. (At design time, as soon as the tooltip component is added, a ‘ToolTip on ToolTip1’ property is automatically available on each control, otherwise in code _SetToolTip()_ must be called for each control.) 


  
  * Control must be active or _ToolTip.ShowAlways = true_





#### The ErrorProvider Component




















    
    <span style="color:blue;">public partial class </span><span style="color:#2b91af;">Form1 </span>: <span style="color:#2b91af;">Form
    </span>{
        <span style="color:#2b91af;">DataTable </span>_dataTable;
    
        <span style="color:#2b91af;">ErrorProvider </span>_errorProvider = <span style="color:blue;">new </span><span style="color:#2b91af;">ErrorProvider</span>();
    
        <span style="color:blue;">public </span>Form1()
        {
            InitializeComponent();
        }
    
        <span style="color:blue;">private void </span>Form1_Load(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
        {
            _errorProvider.ContainerControl = <span style="color:blue;">this</span>;
    
            <span style="color:#2b91af;">TextBox </span>text = <span style="color:blue;">new </span><span style="color:#2b91af;">TextBox</span>();
            <span style="color:blue;">this</span>.Controls.Add(text);
    
            <span style="color:green;">// Diplays a flashing exclamation beside the 
            // control with the text as a tooltip
            </span>_errorProvider.SetError(text, <span style="color:#a31515;">"There's an error here!!"</span>);
    
            <span style="color:green;">// Clear the error
            </span>_errorProvider.SetError(text, <span style="color:#a31515;">""</span>);
    
            <span style="color:green;">// Controls provide a CausesValidation event that can be used
            // to check the data and indicate errors with the ErrorProvider
            </span><span style="color:#2b91af;">TextBox </span>toValidate = <span style="color:blue;">new </span><span style="color:#2b91af;">TextBox</span>();
            toValidate.CausesValidation = <span style="color:blue;">true</span>;
            toValidate.Validating += <span style="color:blue;">new </span><span style="color:#2b91af;">CancelEventHandler</span>(toValidate_Validating);
            <span style="color:blue;">this</span>.Controls.Add(toValidate);
    
            <span style="color:green;">// The ErrorProvider can also be bound to a DataSet to such that
            // errors are indicate on any controls that are also bound
            </span>_errorProvider.DataSource = dataSet1;
            _errorProvider.DataMember = <span style="color:#a31515;">"Customers"</span>;
            _dataTable.Rows[4].SetColumnError(<span style="color:#a31515;">"Name"</span>, <span style="color:#a31515;">"The name must be something else!!"</span>);
            <span style="color:blue;">this</span>.BindingContext[_dataTable].Position = 4; <span style="color:green;">// advance to the error
        </span>}
    
        <span style="color:blue;">void </span>toValidate_Validating(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">CancelEventArgs </span>e)
        {
            <span style="color:#2b91af;">TextBox </span>toCheck = sender <span style="color:blue;">as </span><span style="color:#2b91af;">TextBox</span>;
            <span style="color:blue;">if </span>(toCheck != <span style="color:blue;">null</span>)
            {
                <span style="color:blue;">if </span>(toCheck.Text.Trim().Length == 0)
                {
                    _errorProvider.SetError(toCheck, <span style="color:#a31515;">"The value cannot be blank"</span>);
                }
            }
        }
    
    }


[](http://11011.net/software/vspaste)



#### Configuring the HelpProvider Component





The _HelpProvider_ is usually setup at design time by specifying a HTML Help1.x file (.chm or .html) on the _HelpNamespace_ property. It has methods :






  
  * SetHelpKeyword (set a index or topic keyword for a control which is passed to the help file) 


  
  * SetHelpNavigator (determines how the help is displayed for a control) 


  
  * SetHelpstring (if _HelpNamespace_ is not set, the help string will be displayed in a popup box over the control) 


  
  * SetShowHelp (sets whether help is shown for a particular control) 




    
    <span style="color:#2b91af;">HelpProvider </span>help = <span style="color:blue;">new </span><span style="color:#2b91af;">HelpProvider</span>();
    help.HelpNamespace = <span style="color:#a31515;">@"c:\temp\help.chm"</span>;
    
    <span style="color:#2b91af;">TextBox </span>text = <span style="color:blue;">new </span><span style="color:#2b91af;">TextBox</span>();
    <span style="color:blue;">this</span>.Controls.Add(text);
    help.SetShowHelp(text, <span style="color:blue;">true</span>);
    help.SetHelpKeyword(text, <span style="color:#a31515;">"textcontrol"</span>);
    help.SetHelpNavigator(text, <span style="color:#2b91af;">HelpNavigator</span>.KeywordIndex);
    
    <span style="color:#2b91af;">Button </span>button = <span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>();
    <span style="color:blue;">this</span>.Controls.Add(button);
    help.SetHelpString(button, <span style="color:#a31515;">"This is the button help"</span>);


[](http://11011.net/software/vspaste)



The possible values for the _HelpNavigator_ enumeration :






  
  * AssociateIndex (opens to the index entry for the first letter of the specified keyword) 


  
  * Find (opens to the search page) 


  
  * Index (opens to the index) 


  
  * KeywordIndex (opens to the topic with the specified keyword if exists or the closest matching index entry) 


  
  * TableOfContents (opens to the table of contents) 


  
  * Topic (opens to a specified topic if it exists 


  
  * TopicId (opens to a topic indicated by a numeric topic identifier) 





#### Playing Sounds Files and System Sounds




    
    <span style="color:#2b91af;">SoundPlayer </span>sound = <span style="color:blue;">new </span><span style="color:#2b91af;">SoundPlayer</span>();
    sound.SoundLocation = <span style="color:#a31515;">@"C:\Windows\Media\notify.wav"</span>;
    sound.Play();
    
    <span style="color:#2b91af;">SystemSounds</span>.Asterisk.Play();
    <span style="color:#2b91af;">SystemSounds</span>.Beep.Play();
    <span style="color:#2b91af;">SystemSounds</span>.Exclamation.Play();
    <span style="color:#2b91af;">SystemSounds</span>.Hand.Play();
    <span style="color:#2b91af;">SystemSounds</span>.Question.Play();


[](http://11011.net/software/vspaste)







#### The Timer Component




    
    <span style="color:blue;">private void </span>Form1_Load(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
    {
        System.Windows.Forms.<span style="color:#2b91af;">Timer </span>timer = <span style="color:blue;">new </span>System.Windows.Forms.<span style="color:#2b91af;">Timer</span>();
        timer.Tick += <span style="color:blue;">new </span><span style="color:#2b91af;">EventHandler</span>(timer_Tick);
        timer.Interval = 2000; <span style="color:green;">// milliseconds
        </span>timer.Enabled = <span style="color:blue;">true</span>;
    
        <span style="color:green;">// Or...
        </span>timer.Stop();
        timer.Start();
    }
    
    <span style="color:blue;">void </span>timer_Tick(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
    {
        <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#a31515;">"Timer ticked"</span>);
    }


[](http://11011.net/software/vspaste)



#### The HScrollBar and VScrollBar Controls




    
    <span style="color:blue;">private void </span>Form1_Load(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
    {
        <span style="color:#2b91af;">HScrollBar </span>hscrollbar = <span style="color:blue;">new </span><span style="color:#2b91af;">HScrollBar</span>();
        hscrollbar.Dock = <span style="color:#2b91af;">DockStyle</span>.Bottom;
        hscrollbar.Minimum = 0;
        hscrollbar.Maximum = 100;
        hscrollbar.LargeChange = 20;
        hscrollbar.SmallChange = 1;
        hscrollbar.Scroll += <span style="color:blue;">new </span><span style="color:#2b91af;">ScrollEventHandler</span>(hscrollbar_Scroll);
    
        <span style="color:blue;">this</span>.Controls.Add(hscrollbar);
    }
    
    <span style="color:blue;">void </span>hscrollbar_Scroll(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">ScrollEventArgs </span>e)
    {
        <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#2b91af;">String</span>.Format(<span style="color:#a31515;">"Old value : {0} and new value : {1}"</span>, e.OldValue, e.NewValue));
    }


[](http://11011.net/software/vspaste)



#### Persisting Application Settings Between Sessions





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb.png)](http://philiphendry.files.wordpress.com/2008/10/image.png)





The project properties window allows custom settings to be specified which can have their own types. User settings are read/write whilst application are read only at runtime. The settings must be saved before they are bindable to a property on a control. To bind add a (PropertyBinding) to the (ApplicationSettings) in the properties window for the control :





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb1.png)](http://philiphendry.files.wordpress.com/2008/10/image1.png)





To access the settings programmatically :




    
    <span style="color:blue;">private void </span>Form1_Load(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
    {
        Properties.<span style="color:#2b91af;">Settings</span>.Default.buttonColour = <span style="color:#2b91af;">Color</span>.Red;
        Properties.<span style="color:#2b91af;">Settings</span>.Default.PropertyChanged += <span style="color:blue;">new </span><span style="color:#2b91af;">PropertyChangedEventHandler</span>(Default_PropertyChanged);
        Properties.<span style="color:#2b91af;">Settings</span>.Default.SettingChanging += <span style="color:blue;">new </span>System.Configuration.<span style="color:#2b91af;">SettingChangingEventHandler</span>(Default_SettingChanging);
        Properties.<span style="color:#2b91af;">Settings</span>.Default.SettingsLoaded += <span style="color:blue;">new </span>System.Configuration.<span style="color:#2b91af;">SettingsLoadedEventHandler</span>(Default_SettingsLoaded);
        Properties.<span style="color:#2b91af;">Settings</span>.Default.SettingsSaving += <span style="color:blue;">new </span>System.Configuration.<span style="color:#2b91af;">SettingsSavingEventHandler</span>(Default_SettingsSaving);
        Properties.<span style="color:#2b91af;">Settings</span>.Default.Reload(); <span style="color:green;">// Reloaded from persisted store
        </span>Properties.<span style="color:#2b91af;">Settings</span>.Default.Reset(); <span style="color:green;">// Reset the persisted store to defaults
        </span>Properties.<span style="color:#2b91af;">Settings</span>.Default.Upgrade(); <span style="color:green;">// Upgrade the persisted store to reflect a newer software version
        </span>Properties.<span style="color:#2b91af;">Settings</span>.Default.Save();
    }
    
    <span style="color:blue;">void </span>Default_SettingsSaving(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">CancelEventArgs </span>e)
    {
        e.Cancel = <span style="color:blue;">true</span>;
    }
    
    <span style="color:blue;">void </span>Default_SettingsLoaded(<span style="color:blue;">object </span>sender, System.Configuration.<span style="color:#2b91af;">SettingsLoadedEventArgs </span>e)
    {
        System.Configuration.<span style="color:#2b91af;">SettingsProvider </span>= e.Provider;
    }
    
    <span style="color:blue;">void </span>Default_SettingChanging(<span style="color:blue;">object </span>sender, System.Configuration.<span style="color:#2b91af;">SettingChangingEventArgs </span>e)
    {
        <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#2b91af;">String</span>.Format(<span style="color:#a31515;">"The new value {0} is changing."</span>, e.NewValue));
        e.Cancel = <span style="color:blue;">true</span>;
    }
    
    <span style="color:blue;">void </span>Default_PropertyChanged(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">PropertyChangedEventArgs </span>e)
    {
        <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#2b91af;">String</span>.Format(<span style="color:#a31515;">"The property {0} has changed."</span>, e.PropertyName));
    }


[](http://11011.net/software/vspaste)



## Chapter 13 : Asynchronous Programming Techniques













### Lesson 1 : Managing a Background Process with the BackgroundWorker Component




    
    <span style="color:#2b91af;">BackgroundWorker </span>_async = <span style="color:blue;">new </span><span style="color:#2b91af;">BackgroundWorker</span>();
    
    <span style="color:blue;">private void </span>Form1_Load(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
    {
        _async.WorkerReportsProgress = <span style="color:blue;">true</span>;
        _async.ProgressChanged += <span style="color:blue;">new </span><span style="color:#2b91af;">ProgressChangedEventHandler</span>(_async_ProgressChanged);
        _async.RunWorkerCompleted += <span style="color:blue;">new </span><span style="color:#2b91af;">RunWorkerCompletedEventHandler</span>(_async_RunWorkerCompleted);
        _async.DoWork += <span style="color:blue;">new </span><span style="color:#2b91af;">DoWorkEventHandler</span>(_async_DoWork);
        _async.RunWorkerAsync(<span style="color:#a31515;">"An optional object parameter"</span>);
    
        <span style="color:blue;">if </span>(_async.IsBusy)
            <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#a31515;">"The async processing is busy"</span>);
    
        _async.WorkerSupportsCancellation = <span style="color:blue;">true</span>;
        _async.CancelAsync();
    
        <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#a31515;">"Form_Load completed"</span>);
    }
    
    <span style="color:blue;">void </span>_async_RunWorkerCompleted(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">RunWorkerCompletedEventArgs </span>e)
    {
        <span style="color:blue;">if </span>(e.Cancelled)
            <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#a31515;">"The async job was cancelled"</span>);
        <span style="color:blue;">else
            </span><span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#2b91af;">String</span>.Format(<span style="color:#a31515;">"The result is '{0}'"</span>, e.Result));
    }
    
    <span style="color:blue;">void </span>_async_ProgressChanged(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">ProgressChangedEventArgs </span>e)
    {
        <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#2b91af;">String</span>.Format(<span style="color:#a31515;">"Current progress stands at {0}% with optional state of '{1}'"</span>, 
            e.ProgressPercentage, e.UserState));
    }
    
    <span style="color:blue;">void </span>_async_DoWork(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">DoWorkEventArgs </span>e)
    {
        <span style="color:#2b91af;">Debug</span>.Print(<span style="color:#2b91af;">String</span>.Format(<span style="color:#a31515;">"The parameter is '{0}'"</span>, e.Argument));
        <span style="color:blue;">for </span>(<span style="color:blue;">int </span>percentComplete = 0; percentComplete <= 100; percentComplete += 10)
        {
            _async.ReportProgress(percentComplete, <span style="color:#a31515;">"optional user state object"</span>);
            <span style="color:#2b91af;">Thread</span>.Sleep(1000);
            <span style="color:blue;">if </span>(_async.CancellationPending && percentComplete > 50)
            {
                <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#a31515;">"The async process was canceled"</span>);
                <span style="color:blue;">break</span>;
            }
        }
        e.Result = <span style="color:#a31515;">"An optional result object"</span>;
        <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#a31515;">"Completed a long running process"</span>);
    }


[](http://11011.net/software/vspaste)



### Lesson 2 : Implementing Asynchronous Methods





#### Using Delegates




    
    <span style="color:blue;">delegate string </span><span style="color:#2b91af;">MyDelegate</span>(<span style="color:blue;">object </span>aParameter);
    
    <span style="color:blue;">string </span>MyMethod(<span style="color:blue;">object </span>aParameter)
    {
        <span style="color:#2b91af;">Thread</span>.Sleep(5000);
        <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#2b91af;">String</span>.Format(<span style="color:#a31515;">"The parameter is '{0}'"</span>, aParameter.ToString()));
        <span style="color:blue;">return </span><span style="color:#a31515;">"aReturnString"</span>;
    }
    
    <span style="color:blue;">private void </span>Form1_Load(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
    {
        <span style="color:#2b91af;">MyDelegate </span>mydelegate = <span style="color:blue;">new </span><span style="color:#2b91af;">MyDelegate</span>(MyMethod);
        
        <span style="color:green;">// Synchronously
        </span><span style="color:blue;">string </span>returnValue = mydelegate(<span style="color:#a31515;">"a test string"</span>);
    
        <span style="color:green;">// Asynchronously waiting on EndInvoke
        </span><span style="color:#2b91af;">IAsyncResult </span>asyncResult = mydelegate.BeginInvoke(<span style="color:#a31515;">"another test string"</span>, <span style="color:blue;">null</span>, <span style="color:blue;">null</span>);
        <span style="color:blue;">if </span>(asyncResult.IsCompleted) <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#a31515;">"We've completed"</span>);
        returnValue = mydelegate.EndInvoke(asyncResult);
    
        <span style="color:green;">// Asynchronously waiting on delegate callback
        </span>asyncResult = mydelegate.BeginInvoke(<span style="color:#a31515;">"a third test string"</span>, Callback, mydelegate);
    
        <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#a31515;">"Finished"</span>);
    }
    
    <span style="color:blue;">private void </span>Callback(<span style="color:#2b91af;">IAsyncResult </span>result)
    {
        <span style="color:#2b91af;">Debug</span>.WriteLine(<span style="color:#a31515;">"The callback has been fired"</span>);
        <span style="color:#2b91af;">MyDelegate </span>mydelegate = (<span style="color:#2b91af;">MyDelegate</span>)result.AsyncState;
        mydelegate.EndInvoke(result);
    }





#### Accessing Control Properties from Separate Threads




    
    <span style="color:blue;">public delegate void </span><span style="color:#2b91af;">SetTextDelegate</span>(<span style="color:blue;">string </span>t);
    <span style="color:blue;">public void </span>SetText(<span style="color:blue;">string </span>t)
    {
        <span style="color:green;">// Check to see whether it's safe to set the property directly
        </span><span style="color:blue;">if </span>(_textBox1.InvokeRequired)
        {
            <span style="color:green;">// It's not so we create a delegate to ourself and
            // call Invoke on the form
            </span><span style="color:#2b91af;">SetTextDelegate </span>del = <span style="color:blue;">new </span><span style="color:#2b91af;">SetTextDelegate</span>(SetText);
            <span style="color:blue;">this</span>.Invoke(del, <span style="color:blue;">new object</span>[] { t });
        }
        <span style="color:blue;">else
        </span>{
            _textBox1.Text = t;
        }
    }





## [](http://11011.net/software/vspaste)Chapter 14 : Creating Windows Forms Controls





There are three types of controls :






  
  * user-created controls (composite controls created by combining preexisting controls) 


  
  * custom controls (created from scratch and provide code for rendering) 


  
  * extended controls (add functionality to preexisting controls) 





### Lesson 1 : Creating Composite Controls




    
    <span style="color:green;">// An icon can be specified with an absolute filename, or by specifying a type 
    // (for example a system type with a pre-defined icon) or your own type and the
    // name of an embedded resource.
    </span>[<span style="color:#2b91af;">ToolboxBitmap</span>(<span style="color:blue;">typeof</span>(<span style="color:#2b91af;">UserControl1</span>), <span style="color:#a31515;">"ToolbarIcon.bmp"</span>)]
    <span style="color:blue;">public partial class </span><span style="color:#2b91af;">UserControl1 </span>: <span style="color:#2b91af;">UserControl
    </span>{
        <span style="color:blue;">public </span>UserControl1()
        {
            InitializeComponent();
        }
    
        <span style="color:#2b91af;">TextBox </span>_txtFirstName;
        <span style="color:#2b91af;">TextBox </span>_txtLastName;
        <span style="color:#2b91af;">Button </span>_btnAction;
    
        <span style="color:blue;">public delegate void </span><span style="color:#2b91af;">ActionClick</span>(<span style="color:blue;">string </span>firstname, <span style="color:blue;">string </span>lastname);
        <span style="color:blue;">public event </span><span style="color:#2b91af;">ActionClick </span>Action_Click;
    
        <span style="color:blue;">public string </span>FirstName
        {
            <span style="color:blue;">get </span>{ <span style="color:blue;">return </span>_txtFirstName.Text; }
        }
    
        <span style="color:blue;">public string </span>LastName
        {
            <span style="color:blue;">get </span>{ <span style="color:blue;">return </span>_txtLastName.Text; }
        }
    
        <span style="color:blue;">private void </span>UserControl1_Load(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
        {
            _txtFirstName = <span style="color:blue;">new </span><span style="color:#2b91af;">TextBox</span>();
            _txtFirstName.Location = <span style="color:blue;">new </span><span style="color:#2b91af;">Point</span>(10, 20);
            <span style="color:blue;">this</span>.Controls.Add(_txtFirstName);
    
            _txtLastName = <span style="color:blue;">new </span><span style="color:#2b91af;">TextBox</span>();
            _txtLastName.Location = <span style="color:blue;">new </span><span style="color:#2b91af;">Point</span>(10, 60);
            <span style="color:blue;">this</span>.Controls.Add(_txtLastName);
    
            _btnAction = <span style="color:blue;">new </span><span style="color:#2b91af;">Button</span>();
            _btnAction.Text = <span style="color:#a31515;">"Click for Action!!"</span>;
            _btnAction.Location = <span style="color:blue;">new </span><span style="color:#2b91af;">Point</span>(10, 100);
            _btnAction.Click += <span style="color:blue;">new </span><span style="color:#2b91af;">EventHandler</span>(_btnAction_Click);
            <span style="color:blue;">this</span>.Controls.Add(_btnAction);
        }
    
        <span style="color:blue;">void </span>_btnAction_Click(<span style="color:blue;">object </span>sender, <span style="color:#2b91af;">EventArgs </span>e)
        {
            <span style="color:#2b91af;">ActionClick </span>temp = Action_Click;
            <span style="color:blue;">if </span>(temp != <span style="color:blue;">null</span>)
            {
                temp(FirstName, LastName);
            }
        }
    }


[](http://11011.net/software/vspaste)



[](http://11011.net/software/vspaste)





To create a transparent control and show the contents of the form beneath it :






  
  * this.BackColor = Color.Transparent 

















or to show the screen beneath the form :






  
  * form1.TransparencyKey = ColorRed; 


  
  * myUserControl.BackColor = Color.Red; 





### Lesson 2 : Creating Custom Controls





#### Introduction to the System.Drawing Namespaces






  
  * System.Drawing 


  
  * System.Drawing.Design (design-time graphics operations) 


  
  * System.Drawing.Drawing2D 


  
  * System.Drawing.Imaging 


  
  * System.Drawing.Printing 


  
  * System.Drawing.Text 





#### The Graphics Class





An instance of _Graphics_ represent the drawing surface of a visual element. It cannot be created directly and must be obtained from the owning visual element. All derivatives of _Control_ provide a _CreateGraphics()_ method. Methods on _Graphics _:






  
  * DrawArc 


  
  * DrawClosedCurve 


  
  * DrawCurve 


  
  * DrawEllipse 


  
  * DrawLine 


  
  * DrawLines 


  
  * DrawPath 


  
  * DrawPie 


  
  * DrawPolygon 


  
  * DrawRectangle 


  
  * DrawRectangles 


  
  * DrawString(string, new font(name, size, FontStyle), brush, x, y) 


  
  * FillClosedCurve 


  
  * FillEllipse 


  
  * FillPath 


  
  * FillPie 


  
  * FillPolygon 


  
  * FillRectangle 


  
  * FillRectangles 


  
  * FillRegion 





The Draw### methods above use _Pen_ object whilst the Fill### method use Brush objects. System pens and brushes are available in _SystemPen_ and _SystemBrush_ enumerations.





_Dispose()_ must always be called on _Pen, Brush_ and _Graphics_ objects.




    
    <span style="color:blue;">public partial class </span><span style="color:#2b91af;">CustomControl1 </span>: <span style="color:#2b91af;">Control
    </span>{
        <span style="color:blue;">public </span>CustomControl1()
        {
            InitializeComponent();
        }
    
        <span style="color:blue;">protected override void </span>OnPaint(<span style="color:#2b91af;">PaintEventArgs </span>pe)
        {
            <span style="color:blue;">base</span>.OnPaint(pe);
    
            <span style="color:green;">// The region that needs to be redrawn
            </span><span style="color:#2b91af;">Rectangle </span>clipRectangle = pe.ClipRectangle;
    
            <span style="color:green;">// The graphics object represents the entire control
            </span><span style="color:blue;">using </span>(<span style="color:#2b91af;">Graphics </span>graphics = pe.Graphics)
            {
                graphics.DrawEllipse(<span style="color:#2b91af;">SystemPens</span>.ControlText, clipRectangle);
            }
        }
    }





### [](http://11011.net/software/vspaste)Lesson 3 : Creating Extended Controls and Dialog Boxes





#### Custom Dialog Boxes





To create and show a modal dialog box :




    
    <span style="color:#2b91af;">Form2 </span>myDialog = <span style="color:blue;">new </span><span style="color:#2b91af;">Form2</span>();
    
    <span style="color:green;">// Pass this as the parent of the dialog
    </span><span style="color:#2b91af;">DialogResult </span>dialogResult = myDialog.ShowDialog(<span style="color:blue;">this</span>);
    
    <span style="color:blue;">switch </span>(dialogResult)
    {
        <span style="color:blue;">case </span><span style="color:#2b91af;">DialogResult</span>.Yes:
        <span style="color:blue;">case </span><span style="color:#2b91af;">DialogResult</span>.No:
        <span style="color:blue;">case </span><span style="color:#2b91af;">DialogResult</span>.OK:
        <span style="color:blue;">case </span><span style="color:#2b91af;">DialogResult</span>.Cancel:
        <span style="color:blue;">case </span><span style="color:#2b91af;">DialogResult</span>.Ignore:
        <span style="color:blue;">case </span><span style="color:#2b91af;">DialogResult</span>.Abort:
        <span style="color:blue;">case </span><span style="color:#2b91af;">DialogResult</span>.Retry:
        <span style="color:blue;">case </span><span style="color:#2b91af;">DialogResult</span>.None:
    }





[](http://11011.net/software/vspaste)The dialog itself can access properties on its parent :




    
    <span style="color:#2b91af;">Form2 </span>parentForm = (<span style="color:#2b91af;">Form2</span>)<span style="color:blue;">this</span>.ParentForm;


[](http://11011.net/software/vspaste)



#### Creating Extended Controls




    
    <span style="color:blue;">class </span><span style="color:#2b91af;">ExtendedButton </span>: <span style="color:#2b91af;">Button
    </span>{
        <span style="color:green;">// New properties can be added to the extended control
        </span><span style="color:blue;">public int </span>MyProperty { <span style="color:blue;">get</span>; <span style="color:blue;">set</span>; }
    
        <span style="color:green;">// Base properties and events can also be overridden
        </span><span style="color:blue;">protected override void </span>OnPaint(<span style="color:#2b91af;">PaintEventArgs </span>pevent)
        {
            <span style="color:blue;">base</span>.OnPaint(pevent);
            pevent.Graphics.FillEllipse(<span style="color:#2b91af;">Brushes</span>.Red, pevent.ClipRectangle);
        }
    }





## [](http://11011.net/software/vspaste)Chapter 15 : Deployment





### Lesson 1 : Deploying Applications with ClickOnce

















By default ClickOnce applications run in the Internet or intranet security zones when running from a remote location, or under full trust if installed locally. Additional permissions can be requested by the application.





The ClickOnce app can be configured for online access only, in which case it must be launched from the publishing location, or it can also be available offline in which case it is installed locally along with a start menu icon and add/remove programs entry.





They can optionally check for updates before or after the application starts and either every time the application starts or every set period. A minimum required versions can also be set along with an alternative update location.





The Security tab on the assembly properties can be used to configure extra permissions required in particular zones :





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb2.png)](http://philiphendry.files.wordpress.com/2008/10/image2.png)





Clicking the _Calculate Permissions _button will instruct Visual Studio to analyse the project and list the required permissions.





### Lesson 2 : Creating Setup Projects for Deployment





A _Setup Project_ project type can be added to a solution to configure a installation wizard in a .msi build which can create directories, copy files, modify the registry, and execute custom actions.





In the setup project there are six project editors available from the _View_ menu :





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb3.png)](http://philiphendry.files.wordpress.com/2008/10/image3.png)





The file system editor can be used to create folders on the target machine and assign files to the folders :





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb4.png)](http://philiphendry.files.wordpress.com/2008/10/image4.png)





Files can be added to any of the folders. A folder can be added, an assembly, a file or project ouput :





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb5.png)](http://philiphendry.files.wordpress.com/2008/10/image5.png)





To associate an icon with an application, create a shortcut for the icon then set the shortcut’s _Icon_ property to an icon file. The shortcut can be moved to any other folder.





The following shows how the _VersionNT_ system property is being used to apply a condition to the Primary output, in this case if the target is Windows 2000 :





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb6.png)](http://philiphendry.files.wordpress.com/2008/10/image6.png)

















Properties for the Setup project itself :





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb7.png)](http://philiphendry.files.wordpress.com/2008/10/image7.png)





_ProductCode_ and _UpgradeCode_ are used by the installer and should never be altered. 





#### Configuring a Deployment Project to Deploy the .Net Framework





The _Prerequisites_ button available on the project properties :





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb8.png)](http://philiphendry.files.wordpress.com/2008/10/image8.png)





#### Custom Actions





A custom action must first be implemented in a class derived from _Installer_ component :




    
    <span style="color:green;">// An Installer component can be added by selecting it from
    // the 'Add New Item' menu
    
    </span>[<span style="color:#2b91af;">RunInstaller</span>(<span style="color:blue;">true</span>)]
    <span style="color:blue;">public partial class </span><span style="color:#2b91af;">Installer1 </span>: <span style="color:#2b91af;">Installer
    </span>{
        <span style="color:blue;">public </span>Installer1()
        {
            InitializeComponent();
        }
    
        <span style="color:green;">// There are four operations that can be overridden
    
        </span><span style="color:blue;">public override void </span>Install(<span style="color:#2b91af;">IDictionary </span>stateSaver)
        {
            <span style="color:blue;">base</span>.Install(stateSaver);
        }
    
        <span style="color:blue;">public override void </span>Commit(<span style="color:#2b91af;">IDictionary </span>savedState)
        {
            <span style="color:blue;">base</span>.Commit(savedState);
        }
    
        <span style="color:blue;">public override void </span>Rollback(<span style="color:#2b91af;">IDictionary </span>savedState)
        {
            <span style="color:blue;">base</span>.Rollback(savedState);
        }
    
        <span style="color:blue;">public override void </span>Uninstall(<span style="color:#2b91af;">IDictionary </span>savedState)
        {
            <span style="color:blue;">base</span>.Uninstall(savedState);
        }
    }





[](http://11011.net/software/vspaste)The custom action can then be added to the Custom Action Editor :





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb9.png)](http://philiphendry.files.wordpress.com/2008/10/image9.png)













There are then properties that can be modified for this custom action :






  
  * (Name) 


  
  * Arguments (if the custom action is an executable) 


  
  * Condition 


  
  * CustomActionData 


  
  * EntryPoint (name of the method to execute – but if blank defaults to the name of the action for example_ Install_. Ignored if _InstallerClass = true_) 


  
  * InstallerClass (true if the custom action is implemented in an _Installer_ class) 


  
  * SourcePath (read-only and a path to the action) 





#### Handling Errors in Custom Actions





If an error occurs that cannot be handled then throw a _InstallException(string msg)_ which will cause the installation to be rolled back.
