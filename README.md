# Bind SQLite Data to WinUI DataGrid and Perform CRUD Actions

This sample demonstrates how to bind SQLite database data to a WinUI DataGrid control and perform comprehensive CRUD (Create, Read, Update, Delete) operations with an intuitive user interface.

## Overview

SQLite is a lightweight, file-based relational database that is ideal for desktop applications. In this sample, we demonstrate how to:
- Store employee data in an SQLite database
- Display the data in a WinUI DataGrid with automatic column generation
- Perform CRUD operations through an interactive UI

## SQLite Database Setup

The sample uses SQLite-net-pcl to interact with the SQLite database. The database is initialized in the `DatabasePath` at the application startup:

```csharp
public const string DatabaseFilename = "SQLiteDatabase.db";

public static string DatabasePath =>
    Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), DatabaseFilename);
```

The database automatically creates the employee table when the application starts:

```csharp
public SQLiteDatabase()
{
    _database = new SQLiteAsyncConnection(DatabasePath, Flags);
    _database.CreateTableAsync<Employee>();
}
```

## Data Binding to WinUI DataGrid

The WinUI DataGrid is populated from the SQLite database by loading employee records asynchronously. When the main window is activated, it fetches all employees from the database and binds them to the DataGrid:

```xml
<dataGrid:SfDataGrid DataContext="{StaticResource employeeViewModel}"
                     x:Name="sfDataGrid"
                     AllowEditing="True"
                     ColumnWidthMode="Auto"
                     AutoGenerateColumns="True">
```

The grid automatically generates columns based on the employee data properties and displays all records with auto-fit column widths.

## CRUD Operations

### Create (Add New Record)

Users can add new employee records by clicking the **Add** option from the context menu. This opens the `AddOrEditWindow` dialog:

```csharp
private void OnAddMenuClick(object sender, RoutedEventArgs e)
{
    AddOrEditWindow addWindow = new AddOrEditWindow();
    addWindow.Title = "Add Record";
    App.ShowWindowAtCenter(addWindow.AppWindow, 550, 650);
    addWindow.Activate();
}
```

The form allows entering employee details like ID, Name, Email, Birth Date, Gender, and Location. When saved, the new record is inserted into the SQLite database:

```csharp
if (isEdit)
    await App.Database.UpdateEmployeeAsync(SelectedRecord);
else
    await App.Database.AddEmployeeAsync(SelectedRecord);
```

### Read (Display Records)

All employee records are fetched from the SQLite database and automatically displayed in the DataGrid:

```csharp
sfDataGrid.ItemsSource = await App.Database.GetEmployeesAsync();
```

The `GetEmployeesAsync()` method returns all employee records from the database, which are then bound to the DataGrid for display.

### Update (Edit Existing Record)

Users can edit existing employee records by selecting a record and clicking the **Edit** option:

```csharp
private void OnEditMenuClick(object sender, RoutedEventArgs e)
{
    AddOrEditWindow editWindow = new AddOrEditWindow();
    editWindow.Title = "Edit Record";
    editWindow.SelectedRecord = sfDataGrid.SelectedItem as Employee;
    App.ShowWindowAtCenter(editWindow.AppWindow, 550, 650);
    editWindow.Activate();
}
```

The same form is reused for editing. When saved, the changes are updated in the database:

```csharp
await App.Database.UpdateEmployeeAsync(SelectedRecord);
```

### Delete (Remove Record)

Users can delete employee records by selecting a record and clicking the **Delete** option:

```csharp
private void OnDeleteMenuClick(object sender, RoutedEventArgs e)
{
    DeleteWindow deleteWindow = new DeleteWindow();
    App.ShowWindowAtCenter(deleteWindow.AppWindow, 200, 500);
    deleteWindow.SelectedRecord = sfDataGrid.SelectedItem as Employee;
    deleteWindow.Activate();
}
```

A confirmation dialog appears before deleting. Upon confirmation, the record is removed from the SQLite database:

```csharp
private async void OnYesClick(object sender, RoutedEventArgs e)
{
    await App.Database.DeleteEmployeeAsync(this.SelectedRecord);
    this.Close();
}
```

## Key Features

- **Async Database Operations**: All database operations are asynchronous for better application responsiveness
- **Auto-Generated Columns**: The DataGrid automatically generates columns based on employee data properties
- **Persistent Storage**: All data is persistently stored in the local SQLite database file

## Documentation
Take a moment to peruse the [WinUI SfDataGrid Documentation](https://help.syncfusion.com/winui/datagrid/overview), where you can find about SfDataGrid with code examples.
