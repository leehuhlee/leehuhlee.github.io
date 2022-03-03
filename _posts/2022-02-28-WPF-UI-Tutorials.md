---
layout: post
title: "[WPF] UI Tutorials"
date: 2022-02-28
excerpt: "WPF UI"
tags: [WPF, C#, Visual Studio, XAML, UI]
comments: false
---

# Demo

<figure>
  <a href="/assets/img/posts/wpf_ui_tutorial/demo.jpg"><img src="/assets/img/posts/wpf_ui_tutorial/demo.jpg"></a>
	<figcaption>WPF UI Tutorial Demo</figcaption>
</figure>


# WPF
* What isf WPF?
  - Windows Presentation Foundation (WPF) is a free and open-source graphical subsystem (similar to WinForms) originally developed by Microsoft for rendering user interfaces in Windows-based applications.

* WPF vs WinForms

| WPF | WinForms | 
|---|---|
| It is based on DirectX with XAML support. | It provides access to the native windows library of common controls. |
| It uses markup language for designing UI allowing the design of complex user interfaces. | It does not use a markup language for design. In fact, it uses event-driven controls for the design. |
| It can render fast as compared to WinForms, complexity, and support. | It renders slow as compared to WPF. |
| It can be used to develop and design both windows applications and web applications. | It can only be used to develop and design windows applications. |
| It has unlimited UI customization and controls can be customized easily as it is totally written from scratch. | In this, controls are limited and difficult to customize. |
| It is easier to separate UO from back-end logic. | It is tough to separate UI from back-end logic. |
| It allows you to handle large data sets because of its in-built feature “user interface virtualization”. | It does not have a “user interface virtualization” feature. |
| It takes up more RAM than WinForms. | It takes a smaller memory footprint. |
| It is considered good when the application requires many media types, create a skinned UI, bind to XML, develop a desktop application having a web-like navigation style. | It is considered good if you want to develop an application without much added modern functionality, more online resources. |
| It offers effective and fully supported data binding as compared to WinForms. | It offers data binding but in a limited way. Therefore, it’s less effective as compared to WPF. |
| It also offers rich, interactive, animated, hardware accelerated, vector 2D and 3D capabilities for developing applications. | It does not offer any rich, Interactive, animated, hardware accelerated, vector 2D and 3D capabilities as compared to WPF. |
| It is a little bit tough, time-consuming, and complex to use WPF as compared to WinForms. | It is simple to use WinForms as controls can be used easily, it’s less time-consuming and less tricky as compared to WPF. |
| It has an inbuilt story boarding feature and animation model, it has the ability to use business objects in UI declaratively. | It does not provide such features. |

# Window
  - `xmlns` means xml name pace.
  - `Loaded` invoke event when WPF window machine is loaded.
  - When you add event like `Loaded`, function is automatically added in MainWindow.xaml.cs

# Outline

## Border
  - Draws a border, background, or both around another element.

## StackPanel
  - Arranges child elements into a single line that can be oriented horizontally or vertically.
  - The default value is stretch for both HorizontalAlignment and VerticalAlignment of content that is contained in a StackPanel.

{% highlight xaml %}
<StackPanel>
    <Button Content="Test" />
    <Button Content="Test" />
    <Button Content="Test" />
</StackPanel>
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/wpf_ui_tutorial/StackPanel.jpg"><img src="/assets/img/posts/wpf_ui_tutorial/StackPanel.jpg"></a>
	<figcaption>WPF UI StackPanel</figcaption>
</figure>

## Grid
  - Defines a flexible grid area that consists of columns and rows.
  - If a child element is added to a column within a Grid, and the column has its Width property set to `Auto`, the child will be measured without restrictions. 
  - Columns and rows that are defined within a Grid can take advantage of `*` sizing to distribute remaining space proportionally. When `*` is selected as the height or width of a row or column, that column or row receives a weighted proportion of the remaining available space. This is in contrast to `Auto`, which distributes space evenly based on the size of the content that is within a column or row. This value is expressed as `*` or `2*` when you use Extensible Application Markup Language(XAML).

{% highlight xaml %}
<Grid>
    <Button Content="Test" />
    <Button Content="Test" />
    <Button Content="Test" />
</Grid>
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/wpf_ui_tutorial/Grid.jpg"><img src="/assets/img/posts/wpf_ui_tutorial/Grid.jpg"></a>
	<figcaption>WPF UI Grid</figcaption>
</figure>

## Grid.ColumnDefinitions
  - Each column in the grid is defined using a Column Definition.
  - Columns are positioned in the grid according to the order the Column Definitions are specified in the Grid Options.

{% highlight xaml %}
<StackPanel>
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*">
            <ColumnDefinition Width="*">
            <ColumnDefinition Width="*">
        </Grid.ColumnDefinitions>
        
        <Button Grid.Column="0" Content="Apply" />
        <Button Grid.Column="1" Content="Reset"" />
        <Button Grid.Column="2" Content="Refresh" />

    </Grid>
</StackPanel>
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/wpf_ui_tutorial/Grid.ColumnDefinitions.jpg"><img src="/assets/img/posts/wpf_ui_tutorial/Grid.ColumnDefinitions.jpg"></a>
	<figcaption>WPF UI Grid.ColumnDefinition</figcaption>
</figure>

# Text

## TextBlock
  - Provides a lightweight control for displaying small amounts of flow content.

{% highlight xaml %}
<Border Padding="10">
    <StackPanel>

        <!-- Buttons -->
        <Grid>

            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="*"/>
            </Grid.ColumnDefinitions>

            <Button x:Name="ApplyButton" Click="ApplyButton_Click" Margin="0 0 10 0" Grid.Column="0" Content="Apply" />
            <Button x:Name="ResetButton" Click="ResetButton_Click" Grid.Column="1" Content="Rest" />
            <Button Margin="10 0 0 0" Grid.Column="2" Content="Refresh" />

        </Grid>

        <TextBlock Text="Pulse Properties" FontWeight="Bold" Margin="0 10"/>

        <!-- Description -->
        <TextBlock Text="Description" />
    </StackPanel> 
</Border>
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/wpf_ui_tutorial/TextBlock.jpg"><img src="/assets/img/posts/wpf_ui_tutorial/TextBlock.jpg"></a>
	<figcaption>WPF UI TextBlock</figcaption>
</figure>

# Input
  - `x:name` is a name of the element referense.

## Button
  - `Click` invoke event when the button is clicked.

## TextBox
  - `TextChanged` invoke event when the text in text box is changed.

{% highlight xaml %}
<Border Padding="10">
    <StackPanel>

        <!-- Buttons -->
        <Grid>

            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="*"/>
            </Grid.ColumnDefinitions>

            <Button x:Name="ApplyButton" Click="ApplyButton_Click" Margin="0 0 10 0" Grid.Column="0" Content="Apply" />
            <Button x:Name="ResetButton" Click="ResetButton_Click" Grid.Column="1" Content="Rest" />
            <Button Margin="10 0 0 0" Grid.Column="2" Content="Refresh" />

        </Grid>

        <TextBlock Text="Pulse Properties" FontWeight="Bold" Margin="0 10"/>

        <!-- Description -->
        <TextBlock Text="Description" />
        <TextBox x:Name="DescriptionText" Padding="2" />

        <!-- Status and Revision -->
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <!-- Status -->
            <StackPanel Grid.Column="0" Margin="0 0 10 0">
                <TextBlock Text="Status" />
                <TextBox IsReadOnly="True" Background="#eee" Padding="2" />
            </StackPanel>

            <!-- Revision -->
            <StackPanel Grid.Column="1">
                <TextBlock Text="Revision" />
                <TextBox IsReadOnly="True" Background="#eee" Padding="2" />
            </StackPanel>

        </Grid>

        <!-- Part Number -->
        <TextBlock Text="PArt Number" />
        <TextBox IsReadOnly="True" Background="#eee" Padding="2"/>
    </StackPanel> 
</Border>
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/wpf_ui_tutorial/TextBox.jpg"><img src="/assets/img/posts/wpf_ui_tutorial/TextBox.jpg"></a>
	<figcaption>WPF UI TextBox</figcaption>
</figure>

## ComboBox
  - `SelectionChanged` invoke event when selected value is changed.

{% highlight xaml %}
<Border Padding="10">
    <StackPanel>

        <!-- Buttons -->
        <Grid>

            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="*"/>
            </Grid.ColumnDefinitions>

            <Button x:Name="ApplyButton" Click="ApplyButton_Click" Margin="0 0 10 0" Grid.Column="0" Content="Apply" />
            <Button x:Name="ResetButton" Click="ResetButton_Click" Grid.Column="1" Content="Rest" />
            <Button Margin="10 0 0 0" Grid.Column="2" Content="Refresh" />

        </Grid>

        <TextBlock Text="Pulse Properties" FontWeight="Bold" Margin="0 10"/>

        <!-- Description -->
        <TextBlock Text="Description" />
        <TextBox x:Name="DescriptionText" Padding="2" />

        <!-- Status and Revision -->
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <!-- Status -->
            <StackPanel Grid.Column="0" Margin="0 0 10 0">
                <TextBlock Text="Status" />
                <TextBox IsReadOnly="True" Background="#eee" Padding="2" />
            </StackPanel>

            <!-- Revision -->
            <StackPanel Grid.Column="1">
                <TextBlock Text="Revision" />
                <TextBox IsReadOnly="True" Background="#eee" Padding="2" />
            </StackPanel>

        </Grid>

        <!-- Part Number -->
        <TextBlock Text="PArt Number" />
        <TextBox IsReadOnly="True" Background="#eee" Padding="2"/>

        <!-- Row Material -->
        <TextBlock Text="Raw Material" FontWeight="Bold" Margin="0 10"/>

        <!-- Meterial -->
        <TextBlock Text="Meterial" />
    <ComboBox Padding="2" />
</StackPanel> 
</Border>
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/wpf_ui_tutorial/ComboBox.jpg"><img src="/assets/img/posts/wpf_ui_tutorial/ComboBox.jpg"></a>
	<figcaption>WPF UI ComboBox</figcaption>
</figure>

## CheckBox
  - `Checked` invoke event when check box is checked.

{% highlight xaml %}
<Border Padding="10">
    <StackPanel>

        <!-- Buttons -->
        <Grid>

            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="*"/>
            </Grid.ColumnDefinitions>

            <Button x:Name="ApplyButton" Click="ApplyButton_Click" Margin="0 0 10 0" Grid.Column="0" Content="Apply" />
            <Button x:Name="ResetButton" Click="ResetButton_Click" Grid.Column="1" Content="Rest" />
            <Button Margin="10 0 0 0" Grid.Column="2" Content="Refresh" />

        </Grid>

        <TextBlock Text="Pulse Properties" FontWeight="Bold" Margin="0 10"/>

        <!-- Description -->
        <TextBlock Text="Description" />
        <TextBox x:Name="DescriptionText" Padding="2" />

        <!-- Status and Revision -->
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <!-- Status -->
            <StackPanel Grid.Column="0" Margin="0 0 10 0">
                <TextBlock Text="Status" />
                <TextBox IsReadOnly="True" Background="#eee" Padding="2" />
            </StackPanel>

            <!-- Revision -->
            <StackPanel Grid.Column="1">
                <TextBlock Text="Revision" />
                <TextBox IsReadOnly="True" Background="#eee" Padding="2" />
            </StackPanel>

        </Grid>

        <!-- Part Number -->
        <TextBlock Text="PArt Number" />
        <TextBox IsReadOnly="True" Background="#eee" Padding="2"/>

        <!-- Row Material -->
        <TextBlock Text="Raw Material" FontWeight="Bold" Margin="0 10"/>

        <!-- Meterial -->
        <TextBlock Text="Meterial" />
        <ComboBox Padding="2" />

        <!-- Manufacturing Info -->
        <TextBlock Text="Manufacturing Information" FontWeight="Bold" Margin="0 10"/>

        <!-- Work Centres -->
        <TextBlock Text="Work Centres" Margin="0 0 0 10"/>

        <!-- Checkboxes -->
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <!-- Column 1 -->
            <StackPanel Grid.Column="0" Margin="0 0 10 0">
                <CheckBox Checked="CheckBox_Checked" x:Name="WeldCheckBox" Content="Weld" />
                <CheckBox Checked="CheckBox_Checked" x:Name="AssemblyCheckBox" Content="Assembly" />
                <CheckBox Checked="CheckBox_Checked" x:Name="PlasmaCheckBox" Content="Plasma" />
                <CheckBox Checked="CheckBox_Checked" x:Name="LaserCheckBox" Content="Laser" />
                <CheckBox Checked="CheckBox_Checked" x:Name="PurchaseCheckBox" Content="Purchase" />
            </StackPanel>
            
            <!-- Column 2 -->
            <StackPanel Grid.Column="1" Margin="0 0 10 0">
                <CheckBox Checked="CheckBox_Checked" x:Name="LatheCheckBox" Content="Lathe" />
                <CheckBox Checked="CheckBox_Checked" x:Name="DrillCheckBox" Content="Drill" />
                <CheckBox Checked="CheckBox_Checked" x:Name="FoldCheckBox" Content="Fold" />
                <CheckBox Checked="CheckBox_Checked" x:Name="RollCheckBox" Content="Roll" />
                <CheckBox Checked="CheckBox_Checked" x:Name="SawCheckBox" Content="Saw" />
            </StackPanel>
            
        </Grid>

    </StackPanel> 
</Border>
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/wpf_ui_tutorial/CheckBox.jpg"><img src="/assets/img/posts/wpf_ui_tutorial/CheckBox.jpg"></a>
	<figcaption>WPF UI ComboBox</figcaption>
</figure>

# Modal

## MessageBox
  - Show message box in new window.

{% highlight cs %}
private void ApplyButton_Click(object sender, RoutedEventArgs e)
{
    MessageBox.Show($"The description is: {this.DescriptionText.Text}");
}
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/wpf_ui_tutorial/MessageBox.jpg"><img src="/assets/img/posts/wpf_ui_tutorial/MessageBox.jpg"></a>
	<figcaption>WPF UI MessageBox</figcaption>
</figure>

# Demo

* MainWindow.xaml
{% highlight xaml %}
<Border Padding="10">
    <StackPanel>

        <!-- Buttons -->
        <Grid>

            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="*"/>
                <ColumnDefinition Width="*"/>
            </Grid.ColumnDefinitions>

            <Button x:Name="ApplyButton" Click="ApplyButton_Click" Margin="0 0 10 0" Grid.Column="0" Content="Apply" />
            <Button x:Name="ResetButton" Click="ResetButton_Click" Grid.Column="1" Content="Rest" />
            <Button Margin="10 0 0 0" Grid.Column="2" Content="Refresh" />

        </Grid>

        <TextBlock Text="Pulse Properties" FontWeight="Bold" Margin="0 10"/>

        <!-- Description -->
        <TextBlock Text="Description" />
        <TextBox x:Name="DescriptionText" Padding="2" />

        <!-- Status and Revision -->
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <!-- Status -->
            <StackPanel Grid.Column="0" Margin="0 0 10 0">
                <TextBlock Text="Status" />
                <TextBox IsReadOnly="True" Background="#eee" Padding="2" />
            </StackPanel>

            <!-- Revision -->
            <StackPanel Grid.Column="1">
                <TextBlock Text="Revision" />
                <TextBox IsReadOnly="True" Background="#eee" Padding="2" />
            </StackPanel>

        </Grid>

        <!-- Part Number -->
        <TextBlock Text="PArt Number" />
        <TextBox IsReadOnly="True" Background="#eee" Padding="2"/>

        <!-- Row Material -->
        <TextBlock Text="Raw Material" FontWeight="Bold" Margin="0 10"/>

        <!-- Meterial -->
        <TextBlock Text="Meterial" />
        <ComboBox Padding="2" />

        <!-- Manufacturing Info -->
        <TextBlock Text="Manufacturing Information" FontWeight="Bold" Margin="0 10"/>

        <!-- Work Centres -->
        <TextBlock Text="Work Centres" Margin="0 0 0 10"/>

        <!-- Checkboxes -->
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <!-- Column 1 -->
            <StackPanel Grid.Column="0" Margin="0 0 10 0">
                <CheckBox Checked="CheckBox_Checked" x:Name="WeldCheckBox" Content="Weld" />
                <CheckBox Checked="CheckBox_Checked" x:Name="AssemblyCheckBox" Content="Assembly" />
                <CheckBox Checked="CheckBox_Checked" x:Name="PlasmaCheckBox" Content="Plasma" />
                <CheckBox Checked="CheckBox_Checked" x:Name="LaserCheckBox" Content="Laser" />
                <CheckBox Checked="CheckBox_Checked" x:Name="PurchaseCheckBox" Content="Purchase" />
            </StackPanel>
            
            <!-- Column 2 -->
            <StackPanel Grid.Column="1" Margin="0 0 10 0">
                <CheckBox Checked="CheckBox_Checked" x:Name="LatheCheckBox" Content="Lathe" />
                <CheckBox Checked="CheckBox_Checked" x:Name="DrillCheckBox" Content="Drill" />
                <CheckBox Checked="CheckBox_Checked" x:Name="FoldCheckBox" Content="Fold" />
                <CheckBox Checked="CheckBox_Checked" x:Name="RollCheckBox" Content="Roll" />
                <CheckBox Checked="CheckBox_Checked" x:Name="SawCheckBox" Content="Saw" />
            </StackPanel>
            
        </Grid>

        <!-- Length -->
        <TextBlock Text="Length" Margin="0 10 0 0"/>
        <TextBox x:Name="LengthText" Padding="2" />
        
        <!-- Mass -->
        <TextBlock Text="Mass" Margin="0 10 0 0"/>
        <TextBox x:Name="MassText" Background="#eee" Padding="2" />
        
        <!-- Finish -->
        <TextBlock Text="Finish" Margin="0 10 0 0"/>
        <ComboBox SelectionChanged="FinishDropdown_SelectionChanged"  x:Name="FinishDropdown" SelectedIndex="0" Padding="2">
            <ComboBoxItem>Painted</ComboBoxItem>
            <ComboBoxItem>Not Painted</ComboBoxItem>
        </ComboBox>

        <!-- Purchase Info -->
        <TextBlock Text="Purchase Information" Margin="0 10 0 0"/>
        <ComboBox SelectedIndex="0" Padding="2">
            <ComboBoxItem>Rubber</ComboBoxItem>
            <ComboBoxItem>Not Rubber</ComboBoxItem>
        </ComboBox>

        <!-- Supplier Name -->
        <TextBlock Text="Supplier Name" Margin="0 10 0 0"/>
        <TextBox x:Name="SupplierNameText" TextChanged="SupplierNameText_TextChanged" Padding="2" />

        <!-- Supplier Code -->
        <TextBlock Text="Supplier Code" Margin="0 10 0 0"/>
        <TextBox Padding="2" />

        <TextBlock Text="Additional Information" FontWeight="Bold" Margin="0 10" />
        
        <!-- Note -->
        <TextBlock Text="Note" />
        <TextBox x:Name="NoteText" Padding="2" />
    </StackPanel> 
</Border>
{% endhighlight %}

* MainWindow.xaml.cs
{% highlight cs %}
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
    }

    private void ApplyButton_Click(object sender, RoutedEventArgs e)
    {
        MessageBox.Show($"The description is: {this.DescriptionText.Text}");
    }

    private void ResetButton_Click(object sender, RoutedEventArgs e)
    {
        this.WeldCheckBox.IsChecked = this.AssemblyCheckBox.IsChecked = this.PlasmaCheckBox.IsChecked = this.LaserCheckBox.IsChecked = this.PurchaseCheckBox.IsChecked = this.LaserCheckBox.IsChecked = this.DrillCheckBox.IsChecked = this.FoldCheckBox.IsChecked = this.RollCheckBox.IsChecked = this.SawCheckBox.IsChecked = false;

    }

    private void CheckBox_Checked(object sender, RoutedEventArgs e)
    {
        this.LengthText.Text += ((CheckBox)sender).Content;
    }

    private void FinishDropdown_SelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        if (this.NoteText == null)
            return;

        var combo = (ComboBox)sender;
        var value = (ComboBoxItem)combo.SelectedValue;

        this.NoteText.Text = (string)value.Content;
    }

    private void Window_Loaded(object sender, RoutedEventArgs e)
    {
        FinishDropdown_SelectionChanged(this.FinishDropdown, null);
    }

    private void SupplierNameText_TextChanged(object sender, TextChangedEventArgs e)
    {
        this.MassText.Text = this.SupplierNameText.Text;
    }
}
{% endhighlight %}


# Demo Video

<iframe width="560" height="315" src="/assets/video/posts/wpf_ui_tutorial/Demo.mp4" frameborder="0"> </iframe>

[Download](https://github.com/leehuhlee/WPF){: .btn}