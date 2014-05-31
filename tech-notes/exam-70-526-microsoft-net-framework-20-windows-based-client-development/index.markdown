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

 
    
    [STAThread]
    static void Main()
    {
        Application.EnableVisualStyles();
        Application.SetCompatibleTextRenderingDefault(false);
        Application.Run(new Form1());
    }


[](http://11011.net/software/vspaste)



#### Non-Rectangular Windows Forms




    
    private void Form1_Load(object sender, EventArgs e)
    {
        System.Drawing.Drawing2D.GraphicsPath path = new System.Drawing.Drawing2D.GraphicsPath();
        path.AddEllipse(0, 0, this.Width, this.Height);
        this.FormBorderStyle = FormBorderStyle.None;
        this.Region = new Region(path);
    }


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)







### Lesson 2 : Managing Control Layout with Container Controls





Example container controls : _Panel, FlowLayoutPanel_ and_ SplitContainer. _Changes to properties on the container may also affect all contained controls - for example, setting _Enabled_ on the container disables all contained controls regardless of the individual properties.





#### Controls Collection





Every form and control has a _Controls_ collection. New controls can be added programmatically by creating the control and adding to the collection :




    
    Button button = new Button();
    button.Text = "Press me!";
    button.Location = new Point(20, 20);
    this.Controls.Add(button);


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)







#### Anchor and Dock Properties





The _Anchor_ property can be set to any combination of _Top, Bottom, Left, Right_ or none of these. These will cause different sides of the control to ‘anchor’ to a particular side of it’s containing parent and if opposite sides are specified the control will stretch. For example the following button will stretch horizontally when the form is resized :




    
    button.Anchor = AnchorStyles.Left | AnchorStyles.Right;

















The _Dock_ property can be set to one of _Left, Right, Top, Bottom, Fill _or _None_ and ‘docks’ the control to the parent container :




    
    button.Dock = DockStyle.Top;





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




    
    TableLayoutPanel pnl = new TableLayoutPanel();
    this.Controls.Add(pnl);
    
    pnl.AutoScroll = true;
    pnl.CellBorderStyle = TableLayoutPanelCellBorderStyle.Single;
    pnl.ColumnStyles.Add(new ColumnStyle());
    pnl.ColumnStyles.Add(new ColumnStyle());
    pnl.RowStyles.Add(new RowStyle());
    pnl.RowStyles.Add(new RowStyle());
    Debug.WriteLine(pnl.ColumnCount);
    Debug.WriteLine(pnl.RowCount);
    pnl.ColumnStyles[0].SizeType = SizeType.Percent;
    pnl.ColumnStyles[0].Width = 50;
    pnl.ColumnStyles[1].SizeType = SizeType.Percent;
    pnl.ColumnStyles[1].Width = 50;
    pnl.RowStyles[0].SizeType = SizeType.Absolute;
    pnl.RowStyles[0].Height = 50;
    pnl.RowStyles[1].SizeType = SizeType.Absolute;
    pnl.RowStyles[1].Height = 50;
    pnl.Controls.Add(new Button(), 0, 0);
    pnl.Controls.Add(new Button(), 0, 1);
    pnl.Controls.Add(new Button(), 1, 0);
    pnl.Controls.Add(new Button(), 1, 1);
    
    pnl.GrowStyle = TableLayoutPanelGrowStyle.AddRows;
    pnl.Controls.Add(new Button());


[](http://11011.net/software/vspaste)



#### The TabControl




    
    TabControl tab = new TabControl();
    this.Controls.Add(tab);
    
    tab.TabPages.Add(new TabPage());
    tab.TabPages.Add(new TabPage());
    tab.TabPages[0].Text = "First";
    tab.TabPages[0].AutoScroll = true;
    tab.TabPages[0].BorderStyle = BorderStyle.Fixed3D; // or FixedSingle, None
    
    tab.Appearance = TabAppearance.Normal; // or Buttons, FlatButtons
    tab.Alignment = TabAlignment.Left; // or Top, Bottom, Right
    tab.Multiline = true;


[](http://11011.net/software/vspaste)







#### The SplitContainer




    
    SplitContainer split = new SplitContainer();
    this.Controls.Add(split);
    
    split.Dock = DockStyle.Fill;
    split.Orientation = Orientation.Vertical; // or Horizontal
    split.IsSplitterFixed = false; // if true, use SplitterDistance
    split.SplitterDistance = 200; // in pixels
    split.SplitterWidth = 30;
    split.Panel1MinSize = 40;
    split.Panel2MinSize = 20;
    
    // The following are mutually exclusive!!
    split.Panel1Collapsed = false;
    split.Panel2Collapsed = false;
    
    // FixedPanel decides how to resize panels when the
    // SplitContainer is resized
    split.FixedPanel = FixedPanel.Panel1; // or None, Panel2
    
    // Set the border style of the two SplitterPanels
    split.BorderStyle = BorderStyle.FixedSingle;
    
    // There are two SplitterPanel controls either side of the split
    split.Panel1.Controls.Add(new Button());
    split.Panel2.Controls.Add(new Button());


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

    
  




    
    DialogResult result = this.ShowDialog();


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




    
    SqlConnection sqlConnection = new SqlConnection("Data Source=ServerName;Initial Catalog=DatabaseName;Integrated Security=True");
    
    OleDbConnection oledbConnection = new OleDbConnection("Provider=Microsoft.Jet.OLEDB.4.0;Data Source=\"Nwind.mdb\";Persist Security Info=False");
    
    OdbcConnection odbcConnection = new OdbcConnection("Dsn=MS Access Database;dbq=C:\\DataSource;driverid=281;fil=MS Access;maxbuffersize=2048;pagetimeout=5;uid=admin");
    
    OracleConnection oracleConnection = new OracleConnection("Data Source=Oracle8i;Integrated Security=yes");


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




    
    Configuration config = ConfigurationManager.OpenExeConfiguration(ConfigurationUserLevel.None);
    ConfigurationSection connStrings = config.ConnectionStrings;
    
    // Encrypt the section
    connStrings.SectionInformation.ProtectSection("DataProtectionConfigurationProvider";
    
    // Decrypt the section
    connStrings.SectionInformation.UnprotectSection();
    
    connStrings.SectionInformation.ForceSave = true;
    config.Save(ConfigurationSaveMode.Full);


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




    
    SqlCommand command = new SqlCommand("SELECT pub_id, logo FROM pub_info", connection);
    
    FileStream stream;      // Writes the BLOB to a file (*.bmp).
    BinaryWriter writer;    // Streams the BLOB to the FileStream object.
    
    int bufferSize = 100;                   // Size of the BLOB buffer.
    byte[] outByte = new byte[bufferSize];  // The BLOB byte[] buffer to be filled by GetBytes.
    long retval;                            // The bytes returned from GetBytes.
    long startIndex = 0;                    // The starting position in the BLOB output.
    string pubID = "";                      // The publisher id to use in the file name.
    
    connection.Open();
    SqlDataReader reader = command.ExecuteReader(CommandBehavior.SequentialAccess);
    while (reader.Read())
    {
        pubID = reader.GetString(0);        // Get the publisher id, which must occur before getting the logo.
        stream = new FileStream("logo" + pubID + ".bmp", FileMode.OpenOrCreate, FileAccess.Write);
        writer = new BinaryWriter(stream);
        startIndex = 0;                     // Reset the starting byte for the new BLOB.
    
        // Read bytes into outByte[] and retain the number of bytes returned.
        retval = reader.GetBytes(1, startIndex, outByte, 0, bufferSize);
        while (retval == bufferSize)        // Continue while there are bytes beyond the size of the buffer.
        {
            writer.Write(outByte);
            writer.Flush();
            startIndex += bufferSize;       // Reposition start index to end of last buffer and fill buffer.
            retval = reader.GetBytes(1, startIndex, outByte, 0, bufferSize);
        }
        writer.Write(outByte, 0, (int)retval - 1);   // Write the remaining buffer.
        writer.Flush();         
        writer.Close();
        stream.Close();
    }
    reader.Close();
    connection.Close();


[](http://11011.net/software/vspaste)



#### ‘Chunking’ data updates to BLOBs





BLOBs can be streamed into the database in much the same way as any other data. In the example below photo is a _byte[]_ and the data type has been selected appropriately :




    
    command.Parameters.Add("@Photo", SqlDbType.Image, photo.Length).Value = photo;


[](http://11011.net/software/vspaste)



However, this method can be very intensive therefore there is method of ‘chunking’ the data to reduce the need for resources.The code below has the following features :






  
  * TEXTPTR() sql function is used to retrieve a pointer to a field 


  
  * UPDATETEXT sql is used to insert a byte array at a particular offset 




    
    public static int AddEmployee(string lastName, string firstName, string title, DateTime hireDate, int reportsTo, string photoFilePath)
    {
        using (SqlConnection connection = new SqlConnection("Data Source=(local);Integrated Security=true;Initial Catalog=Northwind;"))
        {
            SqlCommand addEmp = new SqlCommand(
                "INSERT INTO Employees (LastName, FirstName, Title, HireDate, ReportsTo, Photo) " +
                "Values(@LastName, @FirstName, @Title, @HireDate, @ReportsTo, 0x0);" +
                "SELECT @Identity = SCOPE_IDENTITY();" +
                "SELECT @Pointer = TEXTPTR(Photo) FROM Employees WHERE EmployeeID = @Identity", 
                connection);
    
            addEmp.Parameters.Add("@LastName", SqlDbType.NVarChar, 20).Value = lastName;
            addEmp.Parameters.Add("@FirstName", SqlDbType.NVarChar, 10).Value = firstName;
            addEmp.Parameters.Add("@Title", SqlDbType.NVarChar, 30).Value = title;
            addEmp.Parameters.Add("@HireDate", SqlDbType.DateTime).Value = hireDate;
            addEmp.Parameters.Add("@ReportsTo", SqlDbType.Int).Value = reportsTo;
    
            SqlParameter idParm = addEmp.Parameters.Add("@Identity", SqlDbType.Int);
            idParm.Direction = ParameterDirection.Output;
            SqlParameter ptrParm = addEmp.Parameters.Add("@Pointer", SqlDbType.Binary, 16);
            ptrParm.Direction = ParameterDirection.Output;
    
            connection.Open();
            addEmp.ExecuteNonQuery();
            int newEmpID = (int)idParm.Value;
            StorePhoto(photoFilePath, (byte[])ptrParm.Value, connection);
            return newEmpID;
        }
    }


[](http://11011.net/software/vspaste)


    
    public static void StorePhoto(string fileName, byte[] pointer, SqlConnection connection)
    {
        int bufferLen = 128;    // The size of the "chunks" of the image.
        SqlCommand appendToPhoto = new SqlCommand("UPDATETEXT Employees.Photo @Pointer @Offset 0 @Bytes", connection);
        SqlParameter ptrParm = appendToPhoto.Parameters.Add("@Pointer", SqlDbType.Binary, 16);
        ptrParm.Value = pointer;
        SqlParameter photoParm = appendToPhoto.Parameters.Add("@Bytes", SqlDbType.Image, bufferLen);
        SqlParameter offsetParm = appendToPhoto.Parameters.Add("@Offset", SqlDbType.Int);
        offsetParm.Value = 0;
    
        // Read the image in and write it to the database 128 (bufferLen) bytes at a time.
        // Tune bufferLen for best performance. Larger values write faster, but
        // use more system resources.
        FileStream fs = new FileStream(fileName, FileMode.Open, FileAccess.Read);
        BinaryReader br = new BinaryReader(fs);
        byte[] buffer = br.ReadBytes(bufferLen);
        int offset_ctr = 0;
        while (buffer.Length > 0)
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




    
    using (SqlConnection connection = new SqlConnection(connectionString))
    {
        string queryString = @"BULK INSERT Northwind.dbo.[Order Details] " +
            @"FROM 'f:\mydata\data.tbl' " +
            @"WITH ( FORMATFILE='f:\mydata\data.fmt' )";
        connection.Open();
        SqlCommand command = new SqlCommand(queryString, connection);
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




    
    ds.Merge(sourceDS, true, MissingSchemaAction.Add);


[](http://11011.net/software/vspaste)



The _MissingSchemaAction_ defines what should happen if the schemas differ :






  
  * Add (default – all schema items in the source DataSet are added to the target) 


  
  * AddWithKey (all schema items and primary key settings are added to the target) 


  
  * Error (an exception will be thrown if the schema is different) 


  
  * Ignore (all schema differences are ignored) 





#### Example Code to Demonstrate DataSet Features





In the example below the creation of a _DataRelation_ automatically generates a _ForeignKeyConstraint_ and a _UniqueConstraint_.




    
    DataTable ordersTable = new DataTable("orders");
    DataTable orderDetailsTable = new DataTable("orderDetails");
    
    DataSet ds = new DataSet("myDataSet");
    ds.Tables.Add(ordersTable);
    ds.Tables.Add(orderDetailsTable);
    
    // Create an auto-incrementing OrderId column
    ordersTable.Columns.Add("OrderId", Type.GetType("System.Int32"));
    ordersTable.Columns["OrderId"].AutoIncrement = true;
    ordersTable.Columns["OrderId"].AutoIncrementSeed = 100;
    ordersTable.Columns["OrderId"].AutoIncrementStep = 5;
    
    // An example of adding a column expression
    orderDetailsTable.Columns.Add("TotalPrice", Type.GetType("System.Double"), "UnitPrice * Quantity");
    
    // Some more examples of column properties
    orderDetailsTable.Columns.Add("OrderId", Type.GetType("System.Int32"));
    orderDetailsTable.Columns.Add("OrderDetailId", Type.GetType("System.Int32"));
    orderDetailsTable.Columns["OrderDetailId"].AllowDBNull = false;
    orderDetailsTable.Columns["OrderDetailId"].Unique = true;
    
    // Creating a relationship (column data types must match)
    DataRelation relation = new DataRelation("Order_OrderDetails",
        ordersTable.Columns["OrderId"],
        orderDetailsTable.Columns["OrderId"]);
    ds.Relations.Add(relation);
    
    // The relationship can be found on both the child and parent tables
    DataRelation childRelation = ordersTable.ChildRelations["Order_OrderDetails"];
    DataRelation parentRelation = orderDetailsTable.ParentRelations["Order_OrderDetails"];
    
    // Now navigate the relationship using either the object or relationship name
    DataRow[] orderDetailRows = ordersTable.Rows[0].GetChildRows(relation);
    DataRow orderRow = orderDetailsTable.Rows[0].GetParentRow("Order_OrderDetails");
    
    // ALternative method for setting unique key constraint. There are
    // also a variety of overloads for .Add() that offer quicker ways
    // to add the constraint features (e.g. name, data column and primary key bool)
    UniqueConstraint unique = new UniqueConstraint(ordersTable.Columns["OrderId"], true);
    bool isPrimary = unique.IsPrimaryKey; // The primary key boolean is passed in the constructor
    unique.ConstraintName = "PK_Orders_OrderId";
    ordersTable.Constraints.Add(unique);
    
    // Create a Foreign Key between two tables which can be used to force a constraint
    // between them. The DataColumn parameters can also be DataColumn[] if using a composite key
    ForeignKeyConstraint foreignKey = new ForeignKeyConstraint("FK_Orders_OrderDetails",
        ordersTable.Columns["OrderId"],
        orderDetailsTable.Columns["OrderId"]);
    foreignKey.DeleteRule = Rule.Cascade; // or None, SetDefault, SetNull
    foreignKey.UpdateRule = Rule.None;
    foreignKey.AcceptRejectRule = AcceptRejectRule.Cascade; // or None (enforced when calling AcceptChanges())
    ordersTable.Constraints.Add(foreignKey);


[](http://11011.net/software/vspaste)























#### Lesson 3 : Creating DataAdapter Objects





_DataAdapter_ objects are provider-specific and contain connection information and select, update, insert and delete commands to fill _DataTable_ objects and make updates.




    
    SqlConnection conn;
    
    // An example SqlDataAdapter that manually creates each SqlCommand
    SqlDataAdapter adapter = new SqlDataAdapter("SELECT * FROM Customers", conn);
    adapter.InsertCommand = new SqlCommand("INSERT INTO ...", conn);
    adapter.UpdateCommand = new SqlCommand("UPDATE ... SET ...", conn);
    adapter.DeleteCommand = new SqlCommand("DELETE FROM ...", conn);
    // now add any necessary parameters to each SqlCommand parameters collection
    
    // If the select returns data from a single table and the result set contains
    // either a primary key or unique column then the SqlCommandBuilder() can
    // generate the insert, update and delete automatically
    SqlDataAdapter adapter2 = new SqlDataAdapter("SELECT * FROm Customers", conn);
    SqlCommandBuilder commands = new SqlCommandBuilder(adapter2);
    
    // To improve performance commands can be sent to the database in batches
    // The RowUpdating event fires for each row whilst RowUpdated fires once for the batch
    adapter.UpdateBatchSize = 5;
    
    // Action to take if the mappings don't match
    adapter.MissingMappingAction = MissingMappingAction.Passthrough; // or Error, Ignore
    
    // Action to take if a schema change has occured in the db when the select runs
    adapter.MissingSchemaAction = MissingSchemaAction.Add; // or AddWithKey, Error, None


[](http://11011.net/software/vspaste)



#### Filling a DataTable Using an ADO Recordset




    
    String northwindConnectionString = "Provider=SQLOLEDB;Data Source=.\\sqlexpress;Integrated Security=SSPI;Initial Catalog=Northwind";
    ADODB.Recordset rs = new ADODB.Recordset();
    rs.Open("SELECT * FROM Customers", northwindConnectionString, ADODB.CursorTypeEnum.adOpenStatic, ADODB.LockTypeEnum.adLockBatchOptimistic, 0);
    
    DataTable customersTable = new DataTable("Customers");
    System.Data.OleDb.OleDbDataAdapter adapter = new System.Data.OleDb.OleDbDataAdapter();
    adapter.Fill(customersTable, rs);


[](http://11011.net/software/vspaste)











### Lesson 4 : Working with Data in DataTable Objects




    
    // Typed DataSets provide named properties and tables...
    NorthwindDataSet.CustomersRow newRow = (NorthwindDataSet.CustomersRow)northwindDataSet1.Customers.NewRow();
    newRow.CustomerID = "WINGT";
    newRow.CompanyName = "Wingtop Toys";
    newRow.ContactName = "Somebody";
    northwindDataSet1.Customers.Rows.Add(newRow);   // state is Added
    newRow.AcceptChanges();                         // state is now unchanged
    
    // The row state can be :
    //      Unchanged (no changes since AcceptChanges() or Fill() calls)
    //      Added (added since last AcceptChanges())
    //      Modified (updated since last AcceptChanges())
    //      Deleted (deleted since last AcceptChanges())
    //      Detached (not yet been added to a DataTable.Rows collection)
    DataRowState state = newRow.RowState;           // state is Unchanged
    newRow.Delete();                                // state is now deleted
    newRow.AcceptChanges(); // also available on DataTable and DataSet
                                                    // state is now detached
    //newRow.RejectChanges();
    
    // Check and access row errors
    if (northwindDataSet1.Customers.HasErrors)
    {
        DataRow[] erroredRows = northwindDataSet1.Customers.GetErrors();
        Console.WriteLine(erroredRows[0].RowError);
        erroredRows[0].RowError = ""; // Set error to 'processed'
    }


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




    
    TextBox textbox = new TextBox();
    this.Controls.Add(textbox);
    textbox.TextChanged += delegate(object s, EventArgs a) { Debug.WriteLine(String.Format("Textbox changed to {0}", textbox.Text)); };
    textbox.DataBindings.Add("Text", ordersBindingSource, "CustomerID");


[](http://11011.net/software/vspaste)



#### Complex Data Binding





Complex binding binds more than one element of data to more than one property of a control. The following example uses the BendingNavigator toolstrip :





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb19.png)](http://philiphendry.files.wordpress.com/2008/07/image19.png)




    
    BindingNavigator nav = new BindingNavigator(customersBindingSource);
    this.Controls.Add(nav);
    nav.Dock = DockStyle.Top;
    
    DataGrid datagrid = new DataGrid();
    this.Controls.Add(datagrid);
    datagrid.Dock = DockStyle.Fill;
    datagrid.DataSource = customersBindingSource;
    
    // Or without the BindingNavigator you can use the data set...
    //datagrid.DataSource = northwindDataSet1;
    //datagrid.DataMember = "Customers";


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)



#### The BindingSource Class





The _BindingSource_ class provides a level of indirection such that the source of binding to a control can be changed quickly without having to modify all controls :




    
    BindingSource customersBinding = new BindingSource();
    customersBindingSource.DataSource = northwindDataSet1;
    customersBindingSource.DataMember = "Customers";


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




    
    class XmlReaderWriterExamples
    {
        public void XmlReaderExample()
        {
            // A schema set provides a memory cache of schemas for performance benefits
            XmlSchemaSet xmlschemaset = new XmlSchemaSet();
            xmlschemaset.Add("urn:my-schema", "myschema.xsd");
    
            XmlReaderSettings settings = new XmlReaderSettings();
            settings.Schemas.Add("urn:my-schema", "myschema.xsd");   // targetNameSpace, schemaUri
            settings.Schemas = xmlschemaset;    // the alternative if using a schema set
            settings.ValidationType = ValidationType.Schema; // or Auto, None, DTD, XDR
            settings.ConformanceLevel = ConformanceLevel.Auto; // or Document, Fragment
            settings.IgnoreWhitespace = true;
            settings.IgnoreComments = true;
    
            // If you're validating against a DTD then Schemas isn't set but...
            settings.ProhibitDtd = false;   // it needs to be enabled first
            settings.ValidationType = ValidationType.DTD;
    
            // If there's a schema defined in-line in the XML then set the flag..
            settings.ValidationFlags |= XmlSchemaValidationFlags.ProcessInlineSchema;
    
            // Hook up the validation event handling
            settings.ValidationFlags |= XmlSchemaValidationFlags.ReportValidationWarnings;
            settings.ValidationEventHandler += new ValidationEventHandler(settings_ValidationEventHandler);
    
            // Call the static factory method Create() which accepts a Stream, TextReader, XmlReader or Uri
            using (XmlReader xmlreader = XmlReader.Create("books.xml", settings))
            {
                // Call methods in a prescribed manner
                xmlreader.Read();
                xmlreader.ReadStartElement("book");
                xmlreader.ReadStartElement("title");
                Console.Write("The content of the title element:  ");
                Console.WriteLine(xmlreader.ReadString());
                xmlreader.ReadEndElement();
                xmlreader.ReadStartElement("price");
                Console.Write("The content of the price element:  ");
                Console.WriteLine(xmlreader.ReadString());
                xmlreader.ReadEndElement();
                xmlreader.ReadEndElement();
    
                // or, read items and determine the contents on the fly
                while (xmlreader.Read() && !xmlreader.EOF) //don't strictly need EOF check but just for example
                {
                    if (xmlreader.IsStartElement())
                    {
                        if (xmlreader.IsEmptyElement)
                            Console.WriteLine("<{0}/>", xmlreader.Name);
                        else
                        {
                            Console.Write("<{0}> ", xmlreader.Name);
                            xmlreader.Read(); // Read the start tag.
                            if (xmlreader.IsStartElement())  // Handle nested elements.
                                Console.Write("\r\n<{0}>", xmlreader.Name);
                            Console.WriteLine(xmlreader.ReadString());  //Read the text content of the element.
                        }
                    }
                }
    
                // Skips to the next content node or end-of-file if not already a content node
                // Content is : CDATA, Element, EndElement, EntityReference, EndEntity or nonwhite space text
                XmlNodeType contentNode = xmlreader.MoveToContent(); 
    
                xmlreader.Skip(); // Skips children of current node
     
                // Attributes can also be read and when they are, the .Name, .Value properties etc.
                // reflect the attribute instead of the element until repositioned
                if (xmlreader.HasAttributes)
                {
                    Console.WriteLine("Attributes of <" + xmlreader.Name + ">");
                    xmlreader.MoveToFirstAttribute(); // not needed here but an example of the method
                    while (xmlreader.MoveToNextAttribute())
                    {
                        if (xmlreader.HasValue)
                        {
                            Console.WriteLine(" {0}={1}", xmlreader.Name, xmlreader.Value);
                        }
                    }
                    // Move the reader back to the element node.
                    xmlreader.MoveToElement();
                }
    
                if (xmlreader.AttributeCount > 0)
                {
                    Console.WriteLine("The first attribute value : {0}", xmlreader.GetAttribute(0));
                    Console.WriteLine("A named attribute value : {0}", xmlreader.GetAttribute("aName"));
                }
    
                if (xmlreader.NodeType == XmlNodeType.Element)
                {
                    // concatenates all text, significant white space and CDATA until the next markup
                    xmlreader.ReadString();
    
                    // returns all content of the current node including markup (but not the node itself)
                    xmlreader.ReadInnerXml();
    
                    // As Inner but includes the current node
                    xmlreader.ReadOuterXml();
    
                    // The following (for other types too) allow typed data to be retrieved
                    xmlreader.ReadContentAsBoolean();
                    xmlreader.ReadElementContentAsBoolean();
                }
    
            }
        }
    
        void settings_ValidationEventHandler(object sender, ValidationEventArgs e)
        {
            Console.WriteLine("Validation Error : {0} of severity {1}", e.Message, e.Severity);
        }
    }


[](http://11011.net/software/vspaste)



#### The XmlTextReader : XmlReader Class





The _XmlTextReader_ is an implementation of _XmlReader_ and it’s constructor can accept a _Stream, TextReader _or a Url including filenames. Microsoft recommends calling _XmlReader.Create() _instead of using _XmlTextReader_.













#### The XmlNodeReader : XmlReader Class





The _XmlNodeReader _is an implementation of _XmlReader_ that can be used to read a DOM as a stream of XML. However, the recommended best practice is to use and _XmlReader.Create()_ and _XmlReaderSettings_ to take full advantage of all the .Net new features.




    
    XmlDocument doc = new XmlDocument();
    doc.Load("MyXml.xml");
    XmlNodeReader nodereader = new XmlNodeReader(doc);
    while (nodereader.Read())
    {
        // Read the XmlDocument as a stream of XML.
    }





[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)









#### The XmlValidatingReader : XmlReader Class





The _XmlValidatingReader_ is obsolete. It wraps an existing _XmlReader_ via a constructor, and will detect and check inline and referenced schemas. It provides _ValidationType_ property and _ValidationEvent_ event.





#### The XmlWriter Class




    
    using System;
    using System.Xml;
    using System.IO;
    using System.Xml.Schema;
    using System.Text;
    
    class XmlReaderWriterExamples
    {
        public void XmlWriterExample()
        {
            XmlReader xmlreader;
    
            XmlWriterSettings settings = new XmlWriterSettings();
            settings.CheckCharacters = true;    // Raises XmlException if any character is an invalid XML character
            settings.ConformanceLevel = ConformanceLevel.Auto; // or Document, Fragment
            settings.Encoding = Encoding.UTF8;
            settings.Indent = true;
            settings.IndentChars = ("    ");
            settings.NewLineChars = "\r\n";
            settings.NewLineOnAttributes = false;
            settings.OmitXmlDeclaration = false;
    
            using (XmlWriter xmlwriter = XmlWriter.Create("books.xml", settings))
            {
                xmlwriter.WriteComment("This is just a comment for our test file");
    
                // Write XML data.
                xmlwriter.WriteStartElement("book");
    
                // write namespace manually. Subsequent children will have a x: prefix
                xmlwriter.WriteAttributeString("xmlns", "x", null, "urn:1");   // writes xmlns:x="urn:1" to the <Book> element
    
                // An alternative method for writing attributes which makes use of WriteValue() that
                // offers several typed methods for CLR types which are converted using XmlConvert
                xmlwriter.WriteStartAttribute("available");
                xmlwriter.WriteValue(true);
                xmlwriter.WriteEndAttribute();
    
                // Two methods that copy all attributes and elements from an XmlReader
                xmlwriter.WriteAttributes(xmlreader, true);
                xmlwriter.WriteNode(xmlreader, true);
    
                xmlwriter.WriteElementString("price", "19.95");                // writes <x:price>19.95</price>
    
                xmlwriter.WriteEndElement();                                   // writes </Book> use WriteFullEndElement() 
                xmlwriter.Flush();
            }
        }
    }





### [](http://11011.net/software/vspaste)Lesson 2 : Managing XML with the XML Document Object Model





The following diagram highlights the Xml DOM object model with the equivalent W3C class names in brackets.





[![image](http://philiphendry.files.wordpress.com/2008/07/image-thumb20.png)](http://philiphendry.files.wordpress.com/2008/07/image20.png)
















    
    using System;
    using System.Xml;
    using System.IO;
    using System.Xml.Schema;
    using System.Text;
    
    class XmlDocumentExamples
    {
    
        public void XmlDocumentExample()
        {
            XmlReaderSettings xmlreadersettings = new XmlReaderSettings();
            xmlreadersettings.ValidationEventHandler += new ValidationEventHandler(xmlreadersettings_ValidationEventHandler);
    
            XmlReader xmlreader = XmlReader.Create("myinputuri", xmlreadersettings);
    
            XmlDocument xmldocument = new XmlDocument();
            xmldocument.PreserveWhitespace = true;
            xmldocument.LoadXml("<?xml version='1.0'?><books></books>");
            // Alternatively load the data from a Stream, filename, TextReader 
            // or derivative of XmlReader. To validate XML against a schema 
            // during loading a XmlReader must be used in conjunction with 
            // a XmlReaderSettings.
            xmldocument.Load(xmlreader);
    
            // To validate any changes made to the DOM call Validate() and handle
            // and event callbacks. Can also optionally base the node to validate
            // rather than the whole document. 
            xmldocument.Schemas = xmlreadersettings.Schemas;
            xmldocument.Validate(xmlreadersettings_ValidationEventHandler);
    
            // Accessing the root element and creating attributes
            XmlElement root = xmldocument.DocumentElement;
            root.SetAttribute("modified", DateTime.Now.ToString());
    
            // Creating a new element. Also CreateComment, CreateCDataSection, CreateDocumentFragment
            // CreateDocumentType, CreateElement, CreateProcessinInstruction, CreateTextNode,
            // CreateXmlDeclaration, CreateWhitespace, CreateSignificantWhitespace.
            XmlNode newnode1 = xmldocument.CreateElement("book");
            root.AppendChild(newnode1);
    
            // Creating a comment and inserting before
            XmlComment newcomment = xmldocument.CreateComment("The first book");
            root.InsertBefore(newcomment, newnode1); // Insert newnode2 before newnode1
    
            // Cloning a node, adding an attribute and prepending
            XmlElement newnode3 = (XmlElement)newnode1.Clone();
            newnode3.SetAttribute("title", "My First Book");
            root.PrependChild(newnode3); // Insert at the beginning of the child list
    
            // Removal examples for attributes and elements
            root.RemoveAttributeNode("modified", "");
            root.RemoveAllAttributes();
            root.Attributes.RemoveAll();
            root.RemoveAll();
            root.RemoveChild(newnode1);
    
            // For any node inheriting from XmlCharacterData, text can be replaced
            newcomment.ReplaceData(4, 5, "1st");
    
            // Searching
            XmlNodeList nodelist = xmldocument.DocumentElement.FirstChild.SelectNodes(@"//book");
            XmlNode node = xmldocument.DocumentElement.SelectSingleNode("@//book[0]");
    
            // Saving
            xmldocument.Save(@"c:\temp\example.xml"); //or a Stream, TextWriter or XmlWriter
        }
    
        void xmlreadersettings_ValidationEventHandler(object sender, ValidationEventArgs e)
        {
            throw new NotImplementedException();
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




    
    using System;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Data;
    using System.Drawing;
    using System.Linq;
    using System.Text;
    using System.Windows.Forms;
    using System.Diagnostics;
    using System.Drawing.Drawing2D;
    
    namespace WindowsBasedClientDevelopment
    {
        public partial class Form1 : Form
        {
            public Form1()
            {
                InitializeComponent();
            }
    
            private void Form1_Load(object sender, EventArgs e)
            {
                printDocument1.PrintPage += new System.Drawing.Printing.PrintPageEventHandler(printDocument1_PrintPage);
                printDocument1.EndPrint += new System.Drawing.Printing.PrintEventHandler(printDocument1_EndPrint);
            }
    
            void printDocument1_EndPrint(object sender, System.Drawing.Printing.PrintEventArgs e)
            {
                MessageBox.Show("Printing has finished");
            }
    
            private void btnPrint_Click(object sender, EventArgs e)
            {
                printDocument1.Print();
            }
    
            private void btnPreview_Click(object sender, EventArgs e)
            {
                printPreviewDialog1.Document = printDocument1;
                printPreviewDialog1.ShowDialog();
            }
    
            void printDocument1_PrintPage(object sender, System.Drawing.Printing.PrintPageEventArgs e)
            {
                e.Graphics.DrawEllipse(Pens.Black, e.MarginBounds);
    
                GraphicsPath path = new GraphicsPath();
                path.AddPolygon(new Point[] { new Point(1, 1), new Point(12, 55), new Point(34, 8) });
                path.AddRectangle(new Rectangle(33, 43, 20, 20));
                e.Graphics.DrawPath(Pens.Red, path);
    
                Font font = new Font("Tahoma", 12, FontStyle.Regular, GraphicsUnit.Pixel);
                float fontHeight = font.GetHeight(e.Graphics);
                e.Graphics.DrawString("Hello world!", font, Brushes.Firebrick, 200, 200);
    
                e.HasMorePages = false;
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




    
    using System;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Data;
    using System.Drawing;
    using System.Linq;
    using System.Text;
    using System.Windows.Forms;
    using System.Diagnostics;
    using System.Drawing.Drawing2D;
    
    namespace WindowsBasedClientDevelopment
    {
        public partial class Form1 : Form
        {
            public Form1()
            {
                InitializeComponent();
            }
    
            private void Form1_Load(object sender, EventArgs e)
            {
                Button source = new Button();
                source.Text = "Source";
                source.Location = new Point(10, 10);
                source.MouseDown += new MouseEventHandler(source_MouseDown);
    
                Button target = new Button();
                target.Text = "Target";
                target.Location = new Point(50, 50);
                target.AllowDrop = true;
                target.DragEnter += new DragEventHandler(target_DragEnter);
                target.DragDrop += new DragEventHandler(target_DragDrop);
    
                this.Controls.Add(source);
                this.Controls.Add(target);
            }
    
            void target_DragDrop(object sender, DragEventArgs e)
            {
                IDataObject data = e.Data;
                MessageBox.Show(String.Format("Accepted data '{0}'", data.GetData(DataFormats.StringFormat)));
            }
    
            void target_DragEnter(object sender, DragEventArgs e)
            {
                // Check the data is in the format we accept and that the allowed effects
                // contains the effect we accept
                if (e.Data.GetDataPresent(DataFormats.StringFormat) && (e.AllowedEffect & DragDropEffects.Copy) > 0)
                {
                    e.Effect = DragDropEffects.Copy;
                }
            }
    
            void source_MouseDown(object sender, MouseEventArgs e)
            {
                object objectdata = "Just some text";
                Button source = (Button)sender;
    
                // Set the data and allowed effects
                source.DoDragDrop(objectdata, DragDropEffects.Copy | DragDropEffects.Move);
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




    
    // Change the culture used for formatting data
    Thread.CurrentThread.CurrentCulture = new System.Globalization.CultureInfo("fr-CA");
    Console.WriteLine((500).ToString("C")); // Formatted currency using the current culture
    
    // Change the culture used for displaying user interfaces
    Thread.CurrentThread.CurrentUICulture = new System.Globalization.CultureInfo("fr-CA");


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




    
    MenuStrip menu = new MenuStrip();
    ToolStripMenuItem toolstrip = new ToolStripMenuItem("Windows");
    menu.Items.Add(toolstrip);
    menu.MdiWindowListItem = toolstrip;
    
    this.IsMdiContainer = true;
    this.Controls.Add(menu);
    
    Form2 form2 = new Form2();
    form2.MdiParent = this;
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




    
    StatusStrip status = new StatusStrip();
    status.Dock = DockStyle.Bottom;
    
    ToolStripStatusLabel label = new ToolStripStatusLabel("Some status text");
    status.Items.Add(label);
    
    ToolStripProgressBar progress = new ToolStripProgressBar("progress");
    progress.Minimum = 0;
    progress.Maximum = 100;
    progress.Step = 10;
    progress.Value = 40;
    status.Items.Add(progress);
    
    this.Controls.Add(status);





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




















    
    public partial class Form1 : Form
    {
        DataTable _dataTable;
    
        ErrorProvider _errorProvider = new ErrorProvider();
    
        public Form1()
        {
            InitializeComponent();
        }
    
        private void Form1_Load(object sender, EventArgs e)
        {
            _errorProvider.ContainerControl = this;
    
            TextBox text = new TextBox();
            this.Controls.Add(text);
    
            // Diplays a flashing exclamation beside the 
            // control with the text as a tooltip
            _errorProvider.SetError(text, "There's an error here!!");
    
            // Clear the error
            _errorProvider.SetError(text, "");
    
            // Controls provide a CausesValidation event that can be used
            // to check the data and indicate errors with the ErrorProvider
            TextBox toValidate = new TextBox();
            toValidate.CausesValidation = true;
            toValidate.Validating += new CancelEventHandler(toValidate_Validating);
            this.Controls.Add(toValidate);
    
            // The ErrorProvider can also be bound to a DataSet to such that
            // errors are indicate on any controls that are also bound
            _errorProvider.DataSource = dataSet1;
            _errorProvider.DataMember = "Customers";
            _dataTable.Rows[4].SetColumnError("Name", "The name must be something else!!");
            this.BindingContext[_dataTable].Position = 4; // advance to the error
        }
    
        void toValidate_Validating(object sender, CancelEventArgs e)
        {
            TextBox toCheck = sender as TextBox;
            if (toCheck != null)
            {
                if (toCheck.Text.Trim().Length == 0)
                {
                    _errorProvider.SetError(toCheck, "The value cannot be blank");
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




    
    HelpProvider help = new HelpProvider();
    help.HelpNamespace = @"c:\temp\help.chm";
    
    TextBox text = new TextBox();
    this.Controls.Add(text);
    help.SetShowHelp(text, true);
    help.SetHelpKeyword(text, "textcontrol");
    help.SetHelpNavigator(text, HelpNavigator.KeywordIndex);
    
    Button button = new Button();
    this.Controls.Add(button);
    help.SetHelpString(button, "This is the button help");


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




    
    SoundPlayer sound = new SoundPlayer();
    sound.SoundLocation = @"C:\Windows\Media\notify.wav";
    sound.Play();
    
    SystemSounds.Asterisk.Play();
    SystemSounds.Beep.Play();
    SystemSounds.Exclamation.Play();
    SystemSounds.Hand.Play();
    SystemSounds.Question.Play();


[](http://11011.net/software/vspaste)







#### The Timer Component




    
    private void Form1_Load(object sender, EventArgs e)
    {
        System.Windows.Forms.Timer timer = new System.Windows.Forms.Timer();
        timer.Tick += new EventHandler(timer_Tick);
        timer.Interval = 2000; // milliseconds
        timer.Enabled = true;
    
        // Or...
        timer.Stop();
        timer.Start();
    }
    
    void timer_Tick(object sender, EventArgs e)
    {
        Debug.WriteLine("Timer ticked");
    }


[](http://11011.net/software/vspaste)



#### The HScrollBar and VScrollBar Controls




    
    private void Form1_Load(object sender, EventArgs e)
    {
        HScrollBar hscrollbar = new HScrollBar();
        hscrollbar.Dock = DockStyle.Bottom;
        hscrollbar.Minimum = 0;
        hscrollbar.Maximum = 100;
        hscrollbar.LargeChange = 20;
        hscrollbar.SmallChange = 1;
        hscrollbar.Scroll += new ScrollEventHandler(hscrollbar_Scroll);
    
        this.Controls.Add(hscrollbar);
    }
    
    void hscrollbar_Scroll(object sender, ScrollEventArgs e)
    {
        Debug.WriteLine(String.Format("Old value : {0} and new value : {1}", e.OldValue, e.NewValue));
    }


[](http://11011.net/software/vspaste)



#### Persisting Application Settings Between Sessions





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb.png)](http://philiphendry.files.wordpress.com/2008/10/image.png)





The project properties window allows custom settings to be specified which can have their own types. User settings are read/write whilst application are read only at runtime. The settings must be saved before they are bindable to a property on a control. To bind add a (PropertyBinding) to the (ApplicationSettings) in the properties window for the control :





[![image](http://philiphendry.files.wordpress.com/2008/10/image-thumb1.png)](http://philiphendry.files.wordpress.com/2008/10/image1.png)





To access the settings programmatically :




    
    private void Form1_Load(object sender, EventArgs e)
    {
        Properties.Settings.Default.buttonColour = Color.Red;
        Properties.Settings.Default.PropertyChanged += new PropertyChangedEventHandler(Default_PropertyChanged);
        Properties.Settings.Default.SettingChanging += new System.Configuration.SettingChangingEventHandler(Default_SettingChanging);
        Properties.Settings.Default.SettingsLoaded += new System.Configuration.SettingsLoadedEventHandler(Default_SettingsLoaded);
        Properties.Settings.Default.SettingsSaving += new System.Configuration.SettingsSavingEventHandler(Default_SettingsSaving);
        Properties.Settings.Default.Reload(); // Reloaded from persisted store
        Properties.Settings.Default.Reset(); // Reset the persisted store to defaults
        Properties.Settings.Default.Upgrade(); // Upgrade the persisted store to reflect a newer software version
        Properties.Settings.Default.Save();
    }
    
    void Default_SettingsSaving(object sender, CancelEventArgs e)
    {
        e.Cancel = true;
    }
    
    void Default_SettingsLoaded(object sender, System.Configuration.SettingsLoadedEventArgs e)
    {
        System.Configuration.SettingsProvider = e.Provider;
    }
    
    void Default_SettingChanging(object sender, System.Configuration.SettingChangingEventArgs e)
    {
        Debug.WriteLine(String.Format("The new value {0} is changing.", e.NewValue));
        e.Cancel = true;
    }
    
    void Default_PropertyChanged(object sender, PropertyChangedEventArgs e)
    {
        Debug.WriteLine(String.Format("The property {0} has changed.", e.PropertyName));
    }


[](http://11011.net/software/vspaste)



## Chapter 13 : Asynchronous Programming Techniques













### Lesson 1 : Managing a Background Process with the BackgroundWorker Component




    
    BackgroundWorker _async = new BackgroundWorker();
    
    private void Form1_Load(object sender, EventArgs e)
    {
        _async.WorkerReportsProgress = true;
        _async.ProgressChanged += new ProgressChangedEventHandler(_async_ProgressChanged);
        _async.RunWorkerCompleted += new RunWorkerCompletedEventHandler(_async_RunWorkerCompleted);
        _async.DoWork += new DoWorkEventHandler(_async_DoWork);
        _async.RunWorkerAsync("An optional object parameter");
    
        if (_async.IsBusy)
            Debug.WriteLine("The async processing is busy");
    
        _async.WorkerSupportsCancellation = true;
        _async.CancelAsync();
    
        Debug.WriteLine("Form_Load completed");
    }
    
    void _async_RunWorkerCompleted(object sender, RunWorkerCompletedEventArgs e)
    {
        if (e.Cancelled)
            Debug.WriteLine("The async job was cancelled");
        else
            Debug.WriteLine(String.Format("The result is '{0}'", e.Result));
    }
    
    void _async_ProgressChanged(object sender, ProgressChangedEventArgs e)
    {
        Debug.WriteLine(String.Format("Current progress stands at {0}% with optional state of '{1}'", 
            e.ProgressPercentage, e.UserState));
    }
    
    void _async_DoWork(object sender, DoWorkEventArgs e)
    {
        Debug.Print(String.Format("The parameter is '{0}'", e.Argument));
        for (int percentComplete = 0; percentComplete <= 100; percentComplete += 10)
        {
            _async.ReportProgress(percentComplete, "optional user state object");
            Thread.Sleep(1000);
            if (_async.CancellationPending && percentComplete > 50)
            {
                Debug.WriteLine("The async process was canceled");
                break;
            }
        }
        e.Result = "An optional result object";
        Debug.WriteLine("Completed a long running process");
    }


[](http://11011.net/software/vspaste)



### Lesson 2 : Implementing Asynchronous Methods





#### Using Delegates




    
    delegate string MyDelegate(object aParameter);
    
    string MyMethod(object aParameter)
    {
        Thread.Sleep(5000);
        Debug.WriteLine(String.Format("The parameter is '{0}'", aParameter.ToString()));
        return "aReturnString";
    }
    
    private void Form1_Load(object sender, EventArgs e)
    {
        MyDelegate mydelegate = new MyDelegate(MyMethod);
        
        // Synchronously
        string returnValue = mydelegate("a test string");
    
        // Asynchronously waiting on EndInvoke
        IAsyncResult asyncResult = mydelegate.BeginInvoke("another test string", null, null);
        if (asyncResult.IsCompleted) Debug.WriteLine("We've completed");
        returnValue = mydelegate.EndInvoke(asyncResult);
    
        // Asynchronously waiting on delegate callback
        asyncResult = mydelegate.BeginInvoke("a third test string", Callback, mydelegate);
    
        Debug.WriteLine("Finished");
    }
    
    private void Callback(IAsyncResult result)
    {
        Debug.WriteLine("The callback has been fired");
        MyDelegate mydelegate = (MyDelegate)result.AsyncState;
        mydelegate.EndInvoke(result);
    }





#### Accessing Control Properties from Separate Threads




    
    public delegate void SetTextDelegate(string t);
    public void SetText(string t)
    {
        // Check to see whether it's safe to set the property directly
        if (_textBox1.InvokeRequired)
        {
            // It's not so we create a delegate to ourself and
            // call Invoke on the form
            SetTextDelegate del = new SetTextDelegate(SetText);
            this.Invoke(del, new object[] { t });
        }
        else
        {
            _textBox1.Text = t;
        }
    }





## [](http://11011.net/software/vspaste)Chapter 14 : Creating Windows Forms Controls





There are three types of controls :






  
  * user-created controls (composite controls created by combining preexisting controls) 


  
  * custom controls (created from scratch and provide code for rendering) 


  
  * extended controls (add functionality to preexisting controls) 





### Lesson 1 : Creating Composite Controls




    
    // An icon can be specified with an absolute filename, or by specifying a type 
    // (for example a system type with a pre-defined icon) or your own type and the
    // name of an embedded resource.
    [ToolboxBitmap(typeof(UserControl1), "ToolbarIcon.bmp")]
    public partial class UserControl1 : UserControl
    {
        public UserControl1()
        {
            InitializeComponent();
        }
    
        TextBox _txtFirstName;
        TextBox _txtLastName;
        Button _btnAction;
    
        public delegate void ActionClick(string firstname, string lastname);
        public event ActionClick Action_Click;
    
        public string FirstName
        {
            get { return _txtFirstName.Text; }
        }
    
        public string LastName
        {
            get { return _txtLastName.Text; }
        }
    
        private void UserControl1_Load(object sender, EventArgs e)
        {
            _txtFirstName = new TextBox();
            _txtFirstName.Location = new Point(10, 20);
            this.Controls.Add(_txtFirstName);
    
            _txtLastName = new TextBox();
            _txtLastName.Location = new Point(10, 60);
            this.Controls.Add(_txtLastName);
    
            _btnAction = new Button();
            _btnAction.Text = "Click for Action!!";
            _btnAction.Location = new Point(10, 100);
            _btnAction.Click += new EventHandler(_btnAction_Click);
            this.Controls.Add(_btnAction);
        }
    
        void _btnAction_Click(object sender, EventArgs e)
        {
            ActionClick temp = Action_Click;
            if (temp != null)
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




    
    public partial class CustomControl1 : Control
    {
        public CustomControl1()
        {
            InitializeComponent();
        }
    
        protected override void OnPaint(PaintEventArgs pe)
        {
            base.OnPaint(pe);
    
            // The region that needs to be redrawn
            Rectangle clipRectangle = pe.ClipRectangle;
    
            // The graphics object represents the entire control
            using (Graphics graphics = pe.Graphics)
            {
                graphics.DrawEllipse(SystemPens.ControlText, clipRectangle);
            }
        }
    }





### [](http://11011.net/software/vspaste)Lesson 3 : Creating Extended Controls and Dialog Boxes





#### Custom Dialog Boxes





To create and show a modal dialog box :




    
    Form2 myDialog = new Form2();
    
    // Pass this as the parent of the dialog
    DialogResult dialogResult = myDialog.ShowDialog(this);
    
    switch (dialogResult)
    {
        case DialogResult.Yes:
        case DialogResult.No:
        case DialogResult.OK:
        case DialogResult.Cancel:
        case DialogResult.Ignore:
        case DialogResult.Abort:
        case DialogResult.Retry:
        case DialogResult.None:
    }





[](http://11011.net/software/vspaste)The dialog itself can access properties on its parent :




    
    Form2 parentForm = (Form2)this.ParentForm;


[](http://11011.net/software/vspaste)



#### Creating Extended Controls




    
    class ExtendedButton : Button
    {
        // New properties can be added to the extended control
        public int MyProperty { get; set; }
    
        // Base properties and events can also be overridden
        protected override void OnPaint(PaintEventArgs pevent)
        {
            base.OnPaint(pevent);
            pevent.Graphics.FillEllipse(Brushes.Red, pevent.ClipRectangle);
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




    
    // An Installer component can be added by selecting it from
    // the 'Add New Item' menu
    
    [RunInstaller(true)]
    public partial class Installer1 : Installer
    {
        public Installer1()
        {
            InitializeComponent();
        }
    
        // There are four operations that can be overridden
    
        public override void Install(IDictionary stateSaver)
        {
            base.Install(stateSaver);
        }
    
        public override void Commit(IDictionary savedState)
        {
            base.Commit(savedState);
        }
    
        public override void Rollback(IDictionary savedState)
        {
            base.Rollback(savedState);
        }
    
        public override void Uninstall(IDictionary savedState)
        {
            base.Uninstall(savedState);
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
