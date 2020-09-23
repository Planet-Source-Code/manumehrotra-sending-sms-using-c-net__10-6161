<div align="center">

## Sending sms using c\#\.NET


</div>

### Description

SMS is rapidly becoming a major medium of communication. In todays world we come across many examples of services which use SMS as a medium for communication like the polls in different news channels or exams results via SMS. Behind all these services there lies a software which automates the whole process of sending and recieving a SMS.This article discusses a code of a similar software written in C#.NET that sends a sms via a cell phone connected to the COM port. This software makes use of AT commands to send instructions to the cell phone from the computer and requires no third party dll's.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[ManuMehrotra](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/manumehrotra.md)
**Level**          |Intermediate
**User Rating**    |5.0 (10 globes from 2 users)
**Compatibility**  |C\#
**Category**       |[Complete Applications](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/complete-applications__10-7.md)
**World**          |[\.Net \(C\#, VB\.net\)](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/net-c-vb-net.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/manumehrotra-sending-sms-using-c-net__10-6161/archive/master.zip)





### Source Code

```
The code given below sends AT commands to a mobile phone connected to the computer via the COM port and sends a SMS message to the number specified by the user.
using System;
using System.Collections.Generic;
using System.Text;
using System.IO.Ports;
using System.Threading;
namespace ConsoleApplication1
{
class Program
{
static AutoResetEvent readNow = new AutoResetEvent(false);
static SerialPort port;
static void Main(string[] args)
{
try
{
Console.Write("Enter the port name your phone is connected to: ");
string portname = Console.ReadLine();
port = EstablishConnection(portname);
string recievedData = ExecuteCommand("AT", 300);
recievedData = ExecuteCommand("AT+CMGF=1", 300);
Console.Write("Enter the phone number you want to send message to: ");
String phoneNumber = Console.ReadLine();
String command = "AT+CMGS=\"" + phoneNumber + "\"";
Console.Write("Enter the message you want to send: ");
recievedData = ExecuteCommand(command, 300);
string message = Console.ReadLine();
command = message + char.ConvertFromUtf32(26) + "\r";
recievedData = ExecuteCommand(command, 300);
if (recievedData.EndsWith("\r\nOK\r\n"))
recievedData = "Message sent successfully";
if (recievedData.Contains("ERROR"))
{
string recievedError = recievedData;
recievedError = recievedError.Trim();
recievedData = "Following error occured while sending the message" + recievedError;
}
Console.WriteLine(recievedData);
Console.ReadLine();
}
catch (Exception e)
{
Console.WriteLine("Error Message: "+e.Message.Trim()+"\r\nHit any key to Exit");
Console.ReadLine();
}
finally
{
if (port != null)
{
port.Close();
port.DataReceived -= new SerialDataReceivedEventHandler(DataReceived);
port = null;
}
}
}
static string ExecuteCommand(string command,int timeout)
{
port.DiscardInBuffer();
port.DiscardOutBuffer();
readNow.Reset();
port.Write(command+"\r");
string recieved = receive(timeout);
return recieved;
}
static string receive(int timeout)
{
string buffer = string.Empty;
do
{
if (readNow.WaitOne(timeout, false))
{
string t = port.ReadExisting();
buffer += t;
}
}
while (!buffer.EndsWith("\r\nOK\r\n") && !buffer.EndsWith("\r\n> ") && !buffer.Contains("ERROR"));
return buffer;
}
static SerialPort EstablishConnection(string portName)
{
SerialPort port = new SerialPort();
port.PortName = portName;
port.BaudRate = 19200;
port.DataBits = 8;
port.StopBits = StopBits.One;
port.Parity = Parity.None;
port.ReadTimeout = 300;
port.WriteTimeout = 300;
port.Encoding = Encoding.GetEncoding("iso-8859-1");
port.DataReceived += new SerialDataReceivedEventHandler(DataReceived);
port.Open();
port.DtrEnable = true;
port.RtsEnable = true;
return port;
}
static void DataReceived(object sender, SerialDataReceivedEventArgs e)
{
if (e.EventType == SerialData.Chars)
readNow.Set();
}
}
}
```

