# Toolbox translation fork

The `translations` branch builds on the merged upstream redesign. It uses
`akrabi/OpenKNX.Toolbox.Sign` and `akrabi/OpenKNX.Toolbox.Lib`, with their
`translations` branches configured in `.gitmodules`. Gitlinks pin exact commits;
ordinary submodule updates use those pins, not the latest branch tips.

## Build

On Windows with the .NET 9 SDK:

```powershell
git clone --branch translations --recurse-submodules https://github.com/akrabi/OpenKNX.Toolbox.git
Set-Location OpenKNX.Toolbox
dotnet build OpenKNX.Toolbox.sln
dotnet run --project OpenKNX.Toolbox\OpenKNX.Toolbox.csproj
```

For an existing checkout after switching branches:

```powershell
git submodule sync
git submodule update --init --recursive
```

The fork branch must be pushed before it can be cloned from GitHub.

## Generate a bilingual product

Use an extracted release containing its normal `content.xml`, expanded
application XML, and supporting baggage assets. Put English translation JSONs
in a `translations` directory beside the application XML, for example:

```text
data\
  content.xml
  RaumController.xml
  translations\
    en.json
```

Each JSON file contains a `translations` object mapping German text to English.
Only bundle English dictionaries: the signer does not select languages from
metadata. Until OGM-Common release bundling is implemented, supply these files
manually and reload the local release in Toolbox.

Lib discovers the directory; Toolbox forwards it through `ReleaseModel` and
`KnxprodAction` to SignHelper. The release context menu indicates when English
translations will be included. The signer injects language entries before
splitting/signing without modifying the cached source XML. Missing translation
directories preserve the existing generation path; invalid supplied translation
data faults the action through the existing Toolbox error handling.

See the Sign submodule README for supported template substitutions and input
validation. Generating a signed `.knxprod` still requires compatible KNX
conversion/signing assemblies in `%USERPROFILE%\bin\CV` or an ETS installation.
Import the resulting file into ETS for end-to-end acceptance; building Toolbox
alone does not verify signing or ETS display.

The offline producer CLI path and automated release bundling are separate work.
