# Array To Google Sheets #

[![NPM version][npm-image]][npm-url]
[![Test coverage][codecov-image]][codecov-url]
[![David deps][david-image]][david-url]

[npm-image]: https://img.shields.io/npm/v/ts-google-drive.svg
[npm-url]: https://npmjs.org/package/ts-google-drive
[codecov-image]: https://img.shields.io/codecov/c/github/terence410/ts-google-drive.svg?style=flat-square
[codecov-url]: https://codecov.io/gh/terence410/ts-google-drive
[david-image]: https://img.shields.io/david/terence410/ts-google-drive.svg?style=flat-square
[david-url]: https://david-dm.org/terence410/ts-google-drive

Manage google drive easily. Support create folders, upload files, download files, searching, etc..
The library is build with [Google Drive API v3](https://developers.google.com/drive/api/v3/about-sdk).

# Features

- Create Folders
- Upload Files
- Download Files (as Buffer)
- Power file query tools
- Empty Trash

# Usage
```typescript
import * as fs from "fs";
import {TsGooleDrive} from "ts-google-drive";

const tsGoogleDrive = new TsGooleDrive({keyFilename: "serviceAccount.json"});

async function getSingleFile() {
    const fileId = "";
    const file = await tsGoogleDrive.getFile(fileId);
    const isFolder = file.isFolder;
}

async function listFolders() {
    const folderId = "";
    const folders = await tsGoogleDrive
        .query()
        .setFolderOnly()
        .inFolder(folderId)
        .run();
}

async function createFolder() {
    const folderId = "";
    const newFolder = await tsGoogleDrive.createFolder({
        name: "testing",
        parent: folderId,
    });

    // try to search for it again
    const foundFolder = await tsGoogleDrive
        .query()
        .setFolderOnly()
        .setModifiedTime("=", newFolder.modifiedAt)
        .runOnce();
}

async function uploadFile() {
    const folderId = "";
    const filename = "./icon.png";
    const buffer = fs.readFileSync(filename);
    const newFile = await tsGoogleDrive.upload(filename, {parent: folderId});
    const downloadBuffer = await newFile.download();
}

async function search() {
    const folderId = "";
    const query = await tsGoogleDrive
        .query()
        .setFolderOnly()
        .inFolder(folderId)
        .setPageSize(3)
        .setOrderBy("name")
        .setNameContains("New");

    while (query.hasNextPage()) {
        const folders = await query.run();
        for (const folder of folders) {
            await folder.delete();
        }
    }
}

async function emptyTrash() {
    await tsGoogleDrive.emptyTrash();
}
```

# KeyFilename / Service Account

- Create a Google Cloud Project
- [Create Service Account](https://console.cloud.google.com/iam-admin/serviceaccounts/create)
  - Service account details > Choose any service account name > CREATE
  - Grant this service account access to project > CONTINUE
  - Grant users access to this service account ( > CREATE KEY
  - Save the key file into your project
- Enable Drive API & Google Sheets API
  -  [APIs and Services](https://console.cloud.google.com/apis/dashboard) > Enable APIS AND SERVICES 
  - Search Google Drive API > Enable
  - Search Google Sheets API > Enable
- Enable Google Sheets API
- Open the JSON key file, you will find an email xxx@xxx.iam.gserviceaccount.com. 
- Go to your Google Spreadsheets and shared the edit permission to the email address.

# Links
- https://www.npmjs.com/package/google-auth-library
- https://developers.google.com/drive
