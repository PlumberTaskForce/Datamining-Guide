#Star Trek Online Datamining Guide
###Brought to you by John Cena Collective and the Plumber Task Force

This guide was written to introduce you, the reader, to the world of datamining Star Trek Online (and possibly other Cryptic Titles).

##Acknowledgements

You have now violated Perfect World's/Cryptic's Terms of Service. I will not be held responsible for any bans, warning, and other unintended repercussions resulting from thereof.

You have been warned.

##Requirements

As datamining revolves around extracting and otherwise processing game files, you will need lots of hard drive space. I recommend making sure you have 100GB or more on-hand.

From there, you will need certain tools and applications.

* Patched Holodeck (Live) and Tribble (Playtest) installations of Star Trek Online.
* Latest version of [Gibbed's Cryptic Tools](http://svn.gib.me/builds/cryptic/) (b144, 16MB ZIP as of this moment).
* A good text editor, preferably with XML syntax highlighting, full unicode support, and non-windows line ending support. I recommend [Notepad++](https://notepad-plus-plus.org/).
* A tool to compare files. I recommend [WinMerge](http://winmerge.org/?lang=en).
* Administrative permissions on target machine.
* [Microsoft .NET Frawework 4](https://www.microsoft.com/en-us/download/details.aspx?id=17851). Windows 8 and newer come with this preinstalled.
* [Microsoft .NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345). Windows 10 and newer come with this preinstalled. This is only required if you want to make use of MSET tools.
* Optionally, if you're looking to mess with 3D models, our [MSET Decompiler](https://github.com/PlumberTaskForce/MSET-Decompiler/releases/latest).
* Good familiarity with Windows Command Prompt.
* Good familiarity with XML and JSON.
* Familiarity with C# is also good to have, in case you need to fix the tools.

##Step 1: Preparing the environment

The first thing you will need to do is preparing a target environment. The path needs to have a short name (something like C:\STO), or you are going to run into errors during the process.

Inside your directory create the following directories: Hoggs, Tools, Data. When you're done, your directory structure has to look something like this:

    <target directory>
    +- Data
    +- Hoggs
    +- Tools

Inside Tools, create a directory called Gibbed, and extract gibbed's tools there. Make sure that all you move all files to the Gibbed directories, so that the .exe and .dll files end up inside Gibbed.

Next up, inside Gibbed directory, delete everything inside schemas directory.

##Step 2: Preparing the tools

You have the environment set up, good. Next up, you need to set up your tools.

Launch your Holodeck (Live) installation of Star Trek Online, and log in to your account, and then any of your characters.

Now launch your Windows Command Prompt with Administrator permissions. This is very important, as we'll be hooking the STO process, and this cannot be done without admin permissions.

In the command prompt, navigate to <your target environment>\Tools\Gibbed, and run `Gibbed.Cryptic.ExportSchemas.exe`. This will take a couple minutes, and when it's done, you can close your game.

Now comes a tricky part. You will need to make certain modifications to the XML files before you proceed. In the file explorer, go to <your target environment>\Tools\Gibbed\schemas\Star Trek Online\Live\schemas.

When in there, you will want to locate the following files:

* TexWord.schema.xml
* UIGenSMFState.schema.xml
* UIGenTestModelParse.schema.xml

Delete that last file, and open the other 2 in your text editor.

In TexWord, locate a `column` node, with `name` of Size, and change its `type` to IGNORE.

In UIGenSMFState, locate a `column` node with `name` of AnchorBlocks, and change its `type` to IGNORE.

Save both files, and return to the Command Prompt. There, run `Gibbed.Cryptic.GenerateSerializer.exe -g StarTrekOnline Latest.dll`. Wait until the process completes (this will take a while).

When it's done, return to the file explorer, and in it, to Gibbed directory. Locate Latest.dll, and move it to serializers\assemblies.

Then, in the file explorer, go to Gibbed\serializers\resources, and locate ClientMessages.serializer.json. Open the file in your text editor, find the `versions` array, and replace its contents with "Latest". Don't close the file just yet.

This is only necessary with large game updates, and once they happen, you can just regenerate the Latest.dll file, and replace it in the target directory.

##Step 3: Extracting the game archives

For this step, you need to locate your Star Trek Online.

Switch back to Command Prompt. Run `Gibbed.Cryptic.Unpack.exe -v "<path to STO>\Star Trek Online\Playtest\piggs\bins.hogg" "<your target environment>\Hoggs"`. Now this process will take a long time. I recommend you take a break and drink a tea or a coffee.

##Step 4: Extracting the game resources

This set will briefly explain unpacking .bin files to XML.

In the Command Prompt, run `Gibbed.Cryptic.ConvertResource.exe -x "<your target environment>\Hoggs\bin\ClientMessagesEnglish.bin" "<your target environment>\Data\ClientMessages". Note that if the target directory exists, you will need to delete it.

On the first run, the tool will error out saying `Don't know how to handle 'ClientMessagesEnglish'  with a hash of 'somenumber'`. You need to note down this number, and then switch back to your text editor.

In the editor, locate `parser_hash`, and replace its value with the number you noted down. Switch back to Command Prompt and rerun the last command.

Note that editing the .json file is not necessary with each update, just with the major ones.

##Step 5: ???

You have now successfully extracted a game archive, and a game resource. If you go to "<your target environment>\Data\ClientMessages" and open entries.xml, you will find all the clientside language strings STO uses.

Generally, you want to copy this file somewhere else, and then, when an update happens, repeat the unpacking process for your game, and compare the contents with your copy of the contents.xml.

##Textures

Textures in the game generally use 2 formats, one of which has 2 subformats. The formats are .WTEX and .HTEX. The former can contain a .DDS or a .CRN texture, while the latter is a straight-up .CRN texture with changed extension.

For viewing and exporting the textures, I recommend a tool like [Noesis](http://richwhitehouse.com/index.php?content=inc_projects.php).

These files are located in texture.hogg, texture2.hogg, and texture3.hogg.

##Sound files

Sound files in the game are packed into FMod Soundbanks (.FSB files). You can extract those with [FSB Extractor](http://aezay.dk/aezay/fsbextractor/).

These files are located in sound.hogg.

##Models

3D Models in the game use .MSET format. These files can contain several models. To extract them, you can use our [MSET Decompiler](https://github.com/PlumberTaskForce/MSET-Decompiler/releases/latest).

These files are located in bins.hogg.

##Archives

Additional .HOGG files can be extracted using Step 3. Just substitute the .HOGG file name for the one you want to extract.

##Resources

Additional .BIN files can be extracted using Step 4. Just substitute the .BIN file name for the one you want to extract.

These files are located in bins.hogg.

##Video sequences

Video sequences are encoded using Bink Video codec, and have .BIK extension. They can be played using [RAD Video Tools](http://www.radgametools.com/bnkdown.htm) or [VLC Media Player](http://www.videolan.org/vlc/index.html).

These files are located in data.hogg.

##Obtaining other data

Additional data can be obtained by modifying and substituting certain game resources, in order to make the game execute some code that wasn't originally included. This can be used to obtain various server-side information.
