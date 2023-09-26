# C Data Types

> - <https://github.com/jaredpar/pinvoke-interop-assistant>
> - <https://learn.microsoft.com/en-us/shows/funkyonex/p-invoke-interop-assistant>
> - <https://social.msdn.microsoft.com/Forums/en-US/d7cabdc6-e8c9-418f-a1e2-359712cef7ce/data-types-unmanaged-code?forum=csharplanguage>
> - <https://www.displayfusion.com/Discussions/View/converting-c-data-types-to-c/?ID=38db6001-45e5-41a3-ab39-8004450204b3>
> - <https://www.tangiblesoftwaresolutions.com> - proprietary software solutions, with "free edition" version, for transpiling between many languages, also C/C++/C#

## C/C++ Types to C# Types Cheat Sheet

| C# / C++ Type                                                  | C# Type                                         | Size                     |
| -------------------------------------------------------------- | ----------------------------------------------- | ------------------------ |
| BOOL                                                           | bool                                            | 1 byte                   |
| BYTE, unsigned char                                            | byte                                            | 1 byte                   |
| CHAR                                                           | byte                                            | 1 byte                   |
| DECIMAL                                                        | Decimal                                         | 16 bytes                 |
| DOUBLE                                                         | double                                          | 8 bytes                  |
| DWORD, unsigned long, Ulong                                    | Uint, UInt32, \[MarshalAs\(UnmanagedType.U4\)\] | 4 bytes                  |
| FLOAT                                                          | float, single                                   | 4 bytes                  |
| HANDLE, LPDWORD, LPVOID, void\*                                | IntPtr                                          |                          |
| INT16, signed short int                                        | short, Int16                                    | 2 bytes                  |
| INT, INT32, signed int                                         | int, Int32                                      | 4 bytes                  |
| INT64                                                          | long, Int64                                     | 8 bytes                  |
| LONG, LONG32, signed int                                       | int, Int32                                      | 4 bytes                  |
| LONG64                                                         | long, Int64                                     | 8 bytes                  |
| LONGLONG                                                       | long, Int64                                     | 8 bytes                  |
| LP\<struct\>                                                   | \[In\] ref \<struct\>                           |                          |
| LPCTSTR, LPCTSTR, LPSTR, char*, const char*, Wchar_t\*, LPWSTR | String \[in\], StringBuilder \[in, out\]        |                          |
| LPDWORD                                                        | out uint                                        |                          |
| LPTSTR                                                         | \[Out\] StringBuilder                           |                          |
| NULL pointer                                                   | IntPtr.Zero                                     |                          |
| PULARGE_INTEGER                                                | out ulong                                       |                          |
| SHORT, signed short int                                        | short, Int16                                    | 2 bytes                  |
| SIZE_T                                                         | uint                                            |                          |
| UCHAR, unsigned char                                           | byte                                            | 1 byte                   |
| UINT16, WORD                                                   | ushort, UInt16                                  | 2 bytes                  |
| UINT, UINT32, unsigned int                                     | uint, UInt32                                    | 4 bytes                  |
| UINT64                                                         | ulong, UInt64                                   | 8 bytes                  |
| ULONG, unsigned long, ULONG32                                  | uint, UInt32                                    | 4 bytes                  |
| ULONG64                                                        | ulong, UInt64                                   | 8 bytes                  |
| ULONGLONG                                                      | ulong, UInt64                                   | 8 bytes                  |
| void\*, pointers                                               | IntPtr                                          | x86=4 bytes, x64=8 bytes |
| WORD                                                           | ushort, UInt16                                  | 2 bytes                  |
