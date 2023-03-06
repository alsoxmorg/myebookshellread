#myebookshellread - a command line ebook reader shell script

This script is a dedicated to the creator of ebook-speaker
Jos Lemmens / http://jlemmens.nl/ This Script takes no code
from the original but intends to provide quick and easy way
of outputing text to speach and saving the progress of a
text file via a bookmark. it intends to be extreemly portable,
only requring a shell, awk, seq, and espeak ideally you can
replace espeak with any other tts which excepts pipe input.

requirements:
	espeak  - for reading out loud, however could also use flite
	          by changing the variable at teh top of the script
			  from espeak to flite.  you could also bring your
			  own speach syth, if it accepts piped text.
	a shell - tested in bash, sh, yash, dash
	sed     - most shells/distros have this
	awk     - most shells/distros  have this
optional depends:
	additional file formats require external apps to convert
	to plain text files.  most are present in most modern 
	packages managers like debian, fedora or arch
	groff   - for roff/man files
	Latex   - only for detex 'ing documents
	odt2txt - read openoffice files
	ghostscript - for ps2ascii
	pdf2txt - if you dont want to use ghostscript !warning poppler's
	          converter doesnt work yet.  pdf2txt is python-based
			  and required pdfminer.
	epub2txt  see .epub below. may also be in your package manager
	standard compresssion packages, gzip, bzip2, xz, if you compress
	your plain text files.
	

Formats supported
	.txt           - good ole plain text, this is also the 
	                 fallback for any other file
	                 extension be incountered.
	.gz, .bz2, .xz - compressed text.
	.html          - saved web pages.
	.epub          - ebook format, see https://github.com/kevinboone/epub2txt2"
	.ps/pdf        - ghostscipt support is default
	                 but you can also use other converters like pdf2txt (a python
					 script using pdfminer, which *should* exist in most package
					 managers)  Currently this is the fallback for PDF's if 
					 ghostscipt is not installed.
	.odt           - open/libre office document/odt - need to install odt2txt
	.tex           - LaTex format, install Latex suite.
	.1,.2,.ms      - Groff/Troff format, currently -ms supported.

	
How to add your own support.

Add an entry into the bash script
elif [ $FILEARG = *\.xz ];  then #if I find teh file ext
    echo "found a xz file"       #optional debug message
	#find your command string, for example if there is an external program
	#which converts RTF files to txt the command string may look like 
	#RTF2TXT=/usr/bin/rtf2txt
    $XZ2TXT $FILEARG > $RANDOMTMPFILE  #command string, file argument (sent to) a temp file
    FILENAME=$RANDOMTMPFILE  #FILENAME will be used further down in the script
    trap 'rm $RANDOMTMPFILE; exit' INT  #on exit, remove the temp file.


Usage: ebookreader.sh FILENAME LINENUMBER
	Line Number is optional.  If not supplied it will start at line 1
	if a .bookmark file exists, it will start at the line in the file
	if you specifiy your own line it will start there.
	
Limitations:
	* Without TERMCAP or curses, this script cannot accept interactive
	input, like LEFT/RIGHT to go back.  I believe I could create a C
	program with termbox, to emulate this script and add the key
	interactions.

	* REALLY LONG ebookss - i have noticed that it may take a few moments
	for a really long PDF to process all text to a tmp file in order
	to read it.  This is because the script does conversion on the fly
	so you may need to wait.  A more permanent solution would be to
	conver the file once, and keep it in txt format for the duration
	of its use.  so i would ps2acii war_and_peace.pdf > warandpeace.txt
	then read the text file.
