
Project Description:

This has a cut down version of the NAudio library by Mark Heath (https://github.com/naudio/NAudio) and is a command line console app that has audio streams connected to the stdin and stdout, and through processes to convert and encode. It is designed to give output to other programs, or take input from other programs. It can be used for streaming audio over networks and recording to disk. The '-mixer' option gives you all the audio being played by your machine, so a live broadcast encoded into mp3 directly from your desktop, mixing everything including any microphone inputs, is possible.

    this will record an mp3 of what is playing in the background:
      NSox -mixer -mp3 -c 2 -b 16 -r 44100 -br 128 > recording.mp3
 
    this will record and stream its output through the pipe | symbol using the default settings and piping it into another copy of NSox, which is set to play - so playing the input to the ouput
      NSox.exe -rec | NSox.exe -play   
    
    it will play .wav .aif .aiff .mp3 files to stdout, which can also be piped, using the -t option will regulate the output to the correct timing, or it transfers at file copy speed.
      NSox <filename>

this is an example of how you would run NSox as a process from your own C# application and pipe the output over a network:
 
    byte[] buffer = new byte[812];
    int totalbytes;
    string mode;     //System.Net.Sockets.Socket clientSocket; etc.
    //...
    //network socket start
     //...
    System.Diagnostics.Process ChannelProcess = new System.Diagnostics.Process ();
    System.Diagnostics.ProcessStartInfo ChannelInfo = new System.Diagnostics.ProcessStartInfo ();
    ChannelInfo.FileName = "nsox";
    ChannelInfo.Arguments = string.Format ("{0} -c 2 -b 32 -r 192000", mode);
    //or ChannelInfo.Arguments = string.Format("{0} -c 1 -r 22050 -br 64 -mp3", mode);
    ChannelInfo.UseShellExecute = false;
     ChannelInfo.RedirectStandardInput = true;
    ChannelInfo.RedirectStandardOutput = true;
    ChannelInfo.RedirectStandardError = true;
    ChannelProcess.StartInfo = ChannelInfo;  
    ChannelProcess.Start ();
     while (totalbytes > 0 && !ChannelProcess.HasExited && clientSocket.Connected) 
    {
	totalbytes = ChannelProcess.StandardOutput.BaseStream.Read (buffer, 0, buffer.Length);
	//...
	//build packet from 'buffer' and send tcp/udp
	//...
	
Command Line Usage:

            NSox -help (-h)             show this text
            NSox -drivers (-d)          list available codecs
            NSox -rec                   record to stdout
            NSox -play                  play from stdin
            NSox -convert               convert from stdin
            NSox <filename>             play .wav .aif .aiff .mp3 file to stdout
            NSox -tone                  play a sine wave
            NSox -pink                  play pink noise
	    
Options:

            -g722                       apply g722 codec
            -mp3                        apply mp3 codec
            -b                          bits, 8, 16 or 32
            -c                          channels, 1 or 2
            -r                          hz rate, 4000 - 192000
            -br                         kbit/s bitrate, 8 to 320 for mp3 or 48000, 56000, 64000 for G722
            -t                          timed output for files to govern the playback speed to the correct rate through the stdin output
            -ms                         the -rec option audio record milliseconds buffer, 10 - 3000
            --buffer                    the -play option block encode buffer size 256 - 16384 * 4
            -l                          loop play the file
            -f                          frequency for the tone generator 1 - 20000


More Examples:

     mono NSox.exe help (under linux)
     NSox -mixer | NSox -play -c 2 -b 32 -r 44100 (feedback loop)
     NSox -rec -mp3 -c 2 -b 16 -r 44100 -br 128 | >> <app>
     NSox -rec -g722 -br 48000 | NSox -play -g722 -br 48000 (voip test)
     NSox -pink | >> (sound test)
     NSox loop.wav -l -t | NSox -play (loop player)
     
     NSox music.mp3 -t | NSox -convert -mp3 -c 2 -b 16 -r 22050 -br 32 | >> <network>
 
Notes:
 
     MP3, G722 encoding, conversion etc.
     sox 'like' command-line
     partial functionality under mono in linux e.g.
 
     arecord -q -D hw -c 2 -f S16_LE -r 44100 | sox -q --buffer 17 -c 2 -b 16 -r 44100 -e signed -t raw - -c 1 -r 4000 -t raw - | mono NSox.exe -convert -g722 -br 48000 | >> <network>

