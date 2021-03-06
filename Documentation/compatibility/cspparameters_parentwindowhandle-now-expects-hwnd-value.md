## CspParameters.ParentWindowHandle now expects HWND value

### Scope
Minor

### Version Introduced
4.7

### Source Analyzer Status
Investigating

### Change Description
The `CspParameters.ParentWindowHandle` value, introduced in .NET Framework 2.0, allows an application to register a parent window handle value such
that any UI required to access the key (such as a PIN prompt or consent dialog) opens as a modal child to the specified window.

Starting with apps that target the .NET Framework 4.7, a Windows Forms application can set the CspParameters.ParentWindowHandle property with code like the following:

```C#
cspParameters.ParentWindowHandle = form.Handle;
```

In previous versions of the .NET Framework, the value was expected to be an IntPtr representing a location in memory where the
[HWND](https://msdn.microsoft.com/en-us/library/windows/desktop/aa383751(v=vs.85).aspx#HWND) value resided.
Setting the property to form.Handle on Windows 7 and earlier versions had no effect, but on Windows 8 and later versions, it results in a �CryptographicException: The parameter is incorrect.�

- [X] Quirked
- [ ] Build-time break

### Recommended Action
Applications targeting .NET 4.7 or higher wishing to register a parent window relationship are encouraged to use the simplified form:

```C#
cspParameters.ParentWindowHandle = form.Handle;
```

Users who had identified that the correct value to pass was the address of a memory location which held the value `form.Handle` can opt out of the
behavior change by setting the AppContext switch `Switch.System.Security.Cryptography.DoNotAddrOfCspParentWindowHandle` to `true`.

1. By programmatically setting compat switches on the AppContext, as explained [here](http://blogs.msdn.com/b/dotnet/archive/2015/04/29/net-announcements-at-build-2015.aspx#dotnet46)
2. By adding the following line to the `<runtime>` section of the app.config file:

```xml
<runtime>
  <AppContextSwitchOverrides value="Switch.System.Security.Cryptography.DoNotAddrOfCspParentWindowHandle=true"/>
</runtime>
```

Conversely, users who wish to opt in to the new behavior on the .NET Framework 4.7 runtime when the application loads under older .NET Framework versions
can set the AppContext switch to `false`.

### Affected APIs
* `P:System.Security.Cryptography.CspParameters.ParentWindowHandle`

### Category
Security

<!-- breaking change id: 178 -->
