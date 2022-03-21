---
layout: post
title: "[WPF] UI TreeView"
date: 2022-03-03
excerpt: "WPF UI"
tags: [WPF, C#, Visual Studio, XAML, UI]
comments: false
---

# Demo

<figure>
  <a href="/assets/img/posts/wpf_ui_treeview/demo.jpg"><img src="/assets/img/posts/wpf_ui_treeview/demo.jpg"></a>
	<figcaption>WPF UI TreeView Demo</figcaption>
</figure>

# UI

## TreeView
  - TreeView control provides a way to display information in a hierarchical structure by using collapsible nodes.
  - `Folder_Expanded` define what should do when tree folder expanded.

### TreeViewItem
  - a class for a TreeView child.
  - Header : Text of tree item
  - Tag : identifier of tree item

## Style
  - Styles can be defined in several different scopes, depending on where and how you want to use them, and you can even restrict styles to only be used on controls where you explicitly want it.
  - Using the `Resources` section of a control, you can target child controls of this control (and child controls of those child controls and so on).
  - By setting the `x:Key` property on a style, you are telling WPF that you only want to use this style when you explicitly reference it on a specific control. 

### Setter
  - Set something what you want in style

## Image
  - Image control will allow you to display images inside your applications.
  - Using the `Source` property, you can add resource files to your project.

{% highlight xaml %}
  <Grid>
    <TreeView x:Name="FolderView">
      <TreeView.Resources>
        <Style TargetType="{x:Type TreeViewItem}">
          <Setter Property="HeaderTemplate">
            <DataTemplate>
              <StackPanel Orientation="Horizontal">
                <Image Width="20" Margin="3" Source="Image/drive.png"/>
                <TextBlock VerticalAlignment="Center" Text="{Binding}"/>
              </StackPanel>
            </DataTemplate>
          </Setter>
        </Style>
      </TreeView.Resources>
    </TreeView>
  </Grid>
{% endhighlight %}

# Value Convert

## IValueConverter
  - Provides a way to apply custom logic to a binding.
  - When you use image resources on csharp code, you should change image `Build Action` to `Resource` and add `$"pack://application,,,/"`

{% highlight xaml %}
<Image Width="20" Margin="3" Source="{Binding RelativeSource={RelativeSource Mode=FindAncestor, AncestorType={x:Type TreeViewItem}}, Path=Tag, Converter={x:Static local:HeaderToImageConverter.Instance}}"/>
{% endhighlight %}

{% highlight cs %}
[ValueConversion(typeof(string), typeof(BitmapImage))]
public class HeaderToImageConverter : IValueConverter
{
  public static HeaderToImageConverter Instance = new HeaderToImageConverter();

  public object Convert(object value, Type targetType, object parameter, CultureInfo culture){...}
  public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture){...}
}
{% endhighlight %}

# MVVM
  - M : A Model is responsible for exposing data in a way that is easily consumable by WPF. It must implement INotifyPropertyChanged and/or INotifyCollectionChanged as appropriate.
  - V : A View is defined in XAML and should not have any logic in the code-behind. It binds to the view-model by only using data binding.
  - VM : A ViewModel is a model for a view in the application or we can say as abstraction of the view. It exposes data relevant to the view and exposes the behaviors for the views, usually with Commands.
  - It is based on the Model-view-controller pattern (MVC), and is targeted at modern UI development platforms (WPF and Silverlight) in which there is a UX developer who has different requirements than a more "traditional" developer.

## Model

* DirectoryItem.cs
{% highlight cs %}
public class DirectoryItem
{
    public DirectoryItemType Type { get; set; }

    public string FullPath { get; set; }

    public string Name { get { return this.Type == DirectoryItemType.Drive ? this.FullPath : DirectoryStructure.GetFileFolderName(this.FullPath); } }

}
{% endhighlight %}

## View
* MainWindow.xaml
{% highlight xaml %}
<Grid>
      <TreeView x:Name="FolderView" ItemsSource="{Binding Items}">

          <TreeView.ItemContainerStyle>
              <Style TargetType="{x:Type TreeViewItem}">
                  <Setter Property="IsExpanded" Value="{Binding IsExpanded, Mode=TwoWay}"/>
              </Style>
          </TreeView.ItemContainerStyle>

          <TreeView.ItemTemplate>
              <HierarchicalDataTemplate ItemsSource="{Binding Children}">
                  <StackPanel Orientation="Horizontal">
                      <Image Width="20" Margin="3"
                          Source="{Binding Type,
                          Converter={x:Static local:HeaderToImageConverter.Instance}}"/>
                      <TextBlock VerticalAlignment="Center" Text="{Binding Name}"/>
                  </StackPanel>
              </HierarchicalDataTemplate>
          </TreeView.ItemTemplate>

      </TreeView>
  </Grid>
{% endhighlight %}

{% highlight cs %}
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();

        this.DataContext = new DirectoryStructureViewModel();
    }
}
{% endhighlight %}

## ViewModel
* DirectoryStructureViewModel.cs
{% highlight cs %}
public class DirectoryStructureViewModel : BaseViewModel
{
    public ObservableCollection<DirectoryItemViewModel> Items { get; set; }

    public DirectoryStructureViewModel()
    {
        var children = DirectoryStructure.GetLogicalDrives();
        this.Items = new ObservableCollection<DirectoryItemViewModel>(DirectoryStructure.GetLogicalDrives().Select(drive => new DirectoryItemViewModel(drive.FullPath, DirectoryItemType.Drive)));
    }
}
{% endhighlight %}

* DirectoryItemViewModel.cs
{% highlight cs %}
public class DirectoryItemViewModel : BaseViewModel
{
    public DirectoryItemType Type { get; set; }
    
    public string FullPath { get; set; }

    public string Name { get { return this.Type == DirectoryItemType.Drive ? this.FullPath : DirectoryStructure.GetFileFolderName(this.FullPath); } }

    public ObservableCollection<DirectoryItemViewModel> Children { get; set; }

    public bool CanExpand { get { return this.Type != DirectoryItemType.File; } }

    public bool IsExpanded
    {
        get
        {
            return this.Children?.Count(f => f != null) > 0;
        }
        set
        {
            if (value == true)
                Expand();
            else
                this.ClearChildren();
        }
    }

    public ICommand ExpandCommand { get; set; }

    public DirectoryItemViewModel(string fullPath, DirectoryItemType type)
    {
        this.ExpandCommand = new RelayCommand(Expand);

        this.FullPath = fullPath;
        this.Type = type;

        this.ClearChildren();
    }

    private void ClearChildren()
    {
        this.Children = new ObservableCollection<DirectoryItemViewModel>();

        if (this.Type != DirectoryItemType.File)
            this.Children.Add(null);
    }

    private void Expand()
    {
        if (this.Type == DirectoryItemType.File)
            return;

        var children = DirectoryStructure.GetDirectoryContents(this.FullPath);
        this.Children = new ObservableCollection<DirectoryItemViewModel>(
                            children.Select(content => new DirectoryItemViewModel(content.FullPath, content.Type)));
    }
}
{% endhighlight %}

# Demo

<figure>
  <a href="/assets/img/posts/wpf_ui_treeview/demo.jpg"><img src="/assets/img/posts/wpf_ui_treeview/demo.jpg"></a>
	<figcaption>WPF UI TreeView Demo</figcaption>
</figure>

[Download](https://github.com/leehuhlee/WPF){: .btn}