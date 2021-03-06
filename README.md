# XamStorage.Library
A  .NetStandard 2.0 rewrite of dsplaisted [PCLStorage](https://github.com/dsplaisted/PCLStorage).


# Added public directorys to FileSystem

```C#
        /// <summary>
        /// A public folder representing storage which contains Documents, If you enable UIFileSharingEnabled in info.plist on ios app you can share through iTunes.  
        /// </summary>
        Task<IFolder> DocumentsFolderAsync();

        /// <summary>
        /// A public folder representing storage which contains Music. On iOS this Folder is the Documents directory.
        /// </summary>
        Task<IFolder> MusicFolderAsync();

        /// <summary>
        /// A public folder representing storage which contains Pictures. On iOS this Folder is the Documents directory.
        /// </summary>
        Task<IFolder> PicturesFolderAsync();

        /// <summary>
        /// A public folder representing storage which contains Videos. On iOS this Folder is the Documents directory.
        /// </summary>
        Task<IFolder> VideosFolderAsync();
```

# And WriteAsync in IFile 
```C#
        /// <summary>
        /// Writes data to a binary file.
        /// </summary>
        /// <param name="buffer">The buffer to write data from.</param>
        /// <param name="offset">The zero-based byte offset in buffer from which to begin copying bytes to the stream.</param>
        /// <param name="count">The maximum number of bytes to write.</param>
        /// <returns></returns>
        async public Task WriteAsync(byte[] buffer,int offset,int count)
        {
            using (var s = File.Open(Path, FileMode.Open, System.IO.FileAccess.ReadWrite))
            {
                await s.WriteAsync(buffer, offset, count);
            }
        }
```
# Write or read from public directorys

On Android:
In your manifest you need to declare permission READ_EXTERNAL_STORAGE and WRITE_EXTERNAL_STORAGE.

On iOS:
In order to share files through iTunes you need to set UIFileSharing to true (yes).

info.plist
```xml
 <key>UIFileSharingEnabled</key>
 <true/>
```
Since iOS 11 you can also share with Files app.
To make all your files in Documents visible in Files app add

info.plist
```xml
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```


On UWP:
In your appxmanifest  Capabilities, tick the Librarys you are using.
You can´t tick Documents Library there, you have to add that manually together with Filetype Associations.
```xml
 </uap:VisualElements>
 <!-- This -->
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap:FileTypeAssociation Name="xls" DesiredView="default">
            <uap:DisplayName>MyExcelViewer</uap:DisplayName>
            <uap:SupportedFileTypes>
              <uap:FileType>.xls</uap:FileType>
              <uap:FileType>.pdf</uap:FileType>
              <uap:FileType>.html</uap:FileType>
            </uap:SupportedFileTypes>
          </uap:FileTypeAssociation>
        </uap:Extension>
      </Extensions>
      
    </Application>
    
    
    <!-- And in Capabilities -->
    <uap:Capability Name="musicLibrary" />
    <uap:Capability Name="picturesLibrary" />
    <uap:Capability Name="videosLibrary" />
    <uap:Capability Name="documentsLibrary" />
    
```

# Usage
WriteFile in Documents directory Example.
```C#
            IFolder rootFolder =await  FileSystem.Current.DocumentsFolderAsync();
            IFolder folder  = await rootFolder.CreateFolderAsync(SaveFolderName, CreationCollisionOption.OpenIfExists);

            IFile file = await folder.CreateFileAsync((fileName + ".xls").ToSafeFileName(),
                CreationCollisionOption.ReplaceExisting);
                 
                var memoryStream = new MemoryStream();
                //Workbook is an ExcelLibrary that saves in the memoryStream
                Workbook.SaveAs(memoryStream);
                
                var bytes = memoryStream.ToArray();
                await file.WriteAsync(bytes, 0, bytes.Length);
```


Released on Nuget as [XamStorage.Library](https://www.nuget.org/packages/XamFileStorage.Netstandard/)
