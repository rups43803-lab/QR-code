<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">

<meta name="viewport"
      content="width=device-width, initial-scale=1.0">

<title>Shirso QR Generator</title>

<!-- QRCode.js -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    -webkit-tap-highlight-color:transparent;
}

body{
    min-height:100vh;
    background:
    radial-gradient(circle at top,#16435f,#07131f 45%,#02060a);
    color:#fff;
    font-family:Arial,Helvetica,sans-serif;
    padding:18px 14px;
}

.container{
    width:100%;
    max-width:430px;
    margin:auto;
}

.header{
    text-align:center;
    padding:22px 0;
}

.logo{
    font-size:31px;
    font-weight:900;
}

.logo span{
    color:#00d9ff;
}

.subtitle{
    color:#8ea1b5;
    font-size:13px;
    margin-top:7px;
}

.card{
    background:rgba(255,255,255,.07);
    border:1px solid rgba(255,255,255,.12);
    border-radius:25px;
    padding:20px;
    box-shadow:0 20px 60px rgba(0,0,0,.45);
    backdrop-filter:blur(18px);
}

.heading{
    font-size:19px;
    font-weight:800;
    margin-bottom:7px;
}

.desc{
    color:#8496aa;
    font-size:12px;
    line-height:1.5;
    margin-bottom:18px;
}

.input{
    width:100%;
    height:58px;
    padding:0 16px;

    background:rgba(0,0,0,.30);
    border:1px solid rgba(255,255,255,.13);
    border-radius:16px;

    outline:none;
    color:#fff;
    font-size:15px;
}

.input::placeholder{
    color:#687b90;
}

.input:focus{
    border-color:#00d9ff;
    box-shadow:0 0 0 3px rgba(0,217,255,.08);
}

.generate{
    width:100%;
    height:55px;
    margin-top:14px;

    border:0;
    border-radius:16px;

    color:#fff;
    font-size:15px;
    font-weight:800;

    background:linear-gradient(135deg,#00d9ff,#006cff);

    box-shadow:0 10px 25px rgba(0,150,255,.25);
}

.generate:active{
    transform:scale(.98);
}

.message{
    display:none;
    margin-top:12px;
    padding:11px;
    border-radius:12px;
    background:rgba(255,70,70,.12);
    color:#ff9b9b;
    font-size:12px;
    text-align:center;
}

.result{
    display:none;
    margin-top:20px;

    background:#fff;
    border-radius:23px;
    padding:20px;

    text-align:center;
}

.result-title{
    color:#111827;
    font-size:16px;
    font-weight:800;
    margin-bottom:16px;
}

#qrcode{
    width:100%;
    display:flex;
    justify-content:center;
    align-items:center;
}

#qrcode canvas{
    display:block;
    width:260px !important;
    height:260px !important;
}

.link-box{
    margin-top:15px;
    padding:11px;

    border-radius:12px;
    background:#f1f5f9;

    color:#334155;
    font-size:11px;

    word-break:break-all;
}

.download{
    width:100%;
    height:52px;
    margin-top:15px;

    border:0;
    border-radius:15px;

    background:#111827;
    color:#fff;

    font-size:15px;
    font-weight:800;
}

.clear{
    width:100%;
    height:50px;
    margin-top:10px;

    border:0;
    border-radius:15px;

    background:rgba(255,255,255,.07);
    color:#cbd5e1;

    font-size:14px;
    font-weight:700;
}

.footer{
    text-align:center;
    color:#62748a;
    font-size:11px;
    margin-top:20px;
}
</style>
</head>

<body>

<div class="container">

    <div class="header">

        <div class="logo">
            Shirso <span>QR</span>
        </div>

        <div class="subtitle">
            Simple • Fast • Real QR Generator
        </div>

    </div>


    <div class="card">

        <div class="heading">
            Create QR Code
        </div>

        <div class="desc">
            Paste your link or number below. Your exact content
            will be encoded into the QR code.
        </div>


        <input
            id="input"
            class="input"
            type="text"
            placeholder="Paste your link or number"
            autocomplete="off"
            autocapitalize="off"
            spellcheck="false"
        >


        <button
            class="generate"
            onclick="makeQR()"
        >
            ⚡ Generate QR Code
        </button>


        <div
            id="message"
            class="message">
        </div>


        <div
            id="result"
            class="result">

            <div class="result-title">
                ✓ QR Code Ready
            </div>


            <div id="qrcode"></div>


            <div
                id="linkBox"
                class="link-box">
            </div>


            <button
                class="download"
                onclick="downloadQR()">
                ⬇ Download QR Code
            </button>

        </div>


        <button
            class="clear"
            onclick="clearAll()">
            Clear
        </button>

    </div>


    <div class="footer">
        Shirso QR Generator
    </div>

</div>


<script>

/*
====================================================
URL / TEXT PROCESSING
====================================================
*/

function prepareData(value){

    value = value.trim();

    /*
    If it already has a valid protocol,
    keep it exactly as entered.
    */

    if(
        value.startsWith("https://") ||
        value.startsWith("http://") ||
        value.startsWith("tel:") ||
        value.startsWith("mailto:")
    ){
        return value;
    }


    /*
    If it looks like a website,
    automatically add HTTPS.
    */

    if(
        value.startsWith("www.") ||
        value.includes(".com") ||
        value.includes(".net") ||
        value.includes(".org") ||
        value.includes(".bd") ||
        value.includes(".io") ||
        value.includes(".me") ||
        value.includes(".co")
    ){
        return "https://" + value;
    }


    /*
    Otherwise keep it as normal text/number.
    */

    return value;
}


/*
====================================================
GENERATE QR
====================================================
*/

function makeQR(){

    const input =
        document.getElementById("input");

    const result =
        document.getElementById("result");

    const qr =
        document.getElementById("qrcode");

    const message =
        document.getElementById("message");

    const linkBox =
        document.getElementById("linkBox");


    let value =
        input.value.trim();


    /*
    Empty input
    */

    if(value === ""){

        message.style.display="block";

        message.textContent =
        "Please paste your link or number first.";

        input.focus();

        return;
    }


    message.style.display="none";


    /*
    Convert website into a real URL.
    */

    const finalData =
        prepareData(value);


    /*
    IMPORTANT:
    Delete the previous QR completely.
    */

    qr.innerHTML="";


    /*
    Generate a NEW QR using the user's
    actual input as the QR DATA.
    */

    new QRCode(
        qr,
        {
            text:finalData,

            width:520,
            height:520,

            colorDark:"#000000",
            colorLight:"#ffffff",

            correctLevel:
            QRCode.CorrectLevel.H
        }
    );


    /*
    Show the exact data encoded.
    */

    linkBox.textContent =
        finalData;


    /*
    Show result.
    */

    result.style.display="block";


    /*
    Scroll to QR.
    */

    setTimeout(function(){

        result.scrollIntoView({
            behavior:"smooth",
            block:"center"
        });

    },100);

}


/*
====================================================
DOWNLOAD
====================================================
*/

function downloadQR(){

    const canvas =
        document.querySelector("#qrcode canvas");


    if(!canvas){

        alert("Please generate a QR code first.");

        return;
    }


    /*
    Create a high-quality PNG.
    */

    const downloadCanvas =
        document.createElement("canvas");

    const size = 1040;

    downloadCanvas.width=size;
    downloadCanvas.height=size;


    const ctx =
        downloadCanvas.getContext("2d");


    /*
    White background.
    */

    ctx.fillStyle="#ffffff";

    ctx.fillRect(
        0,
        0,
        size,
        size
    );


    /*
    Draw QR at high resolution.
    */

    ctx.imageSmoothingEnabled=false;

    ctx.drawImage(
        canvas,
        0,
        0,
        size,
        size
    );


    /*
    Download.
    */

    const link =
        document.createElement("a");

    link.download =
        "Shirso-QR-Code.png";

    link.href =
        downloadCanvas.toDataURL(
            "image/png"
        );

    document.body.appendChild(link);

    link.click();

    document.body.removeChild(link);

}


/*
====================================================
CLEAR
====================================================
*/

function clearAll(){

    document.getElementById("input").value="";

    document.getElementById("qrcode").innerHTML="";

    document.getElementById("linkBox").textContent="";

    document.getElementById("result").style.display="none";

    document.getElementById("message").style.display="none";

    document.getElementById("input").focus();

}

</script>

</body>
</html><!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">

<meta name="viewport"
      content="width=device-width, initial-scale=1.0">

<title>Shirso QR Generator</title>

<!-- QRCode.js -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>

<style>
*{
    margin:0;
    padding:0;
    box-sizing:border-box;
    -webkit-tap-highlight-color:transparent;
}

body{
    min-height:100vh;
    background:
    radial-gradient(circle at top,#16435f,#07131f 45%,#02060a);
    color:#fff;
    font-family:Arial,Helvetica,sans-serif;
    padding:18px 14px;
}

.container{
    width:100%;
    max-width:430px;
    margin:auto;
}

.header{
    text-align:center;
    padding:22px 0;
}

.logo{
    font-size:31px;
    font-weight:900;
}

.logo span{
    color:#00d9ff;
}

.subtitle{
    color:#8ea1b5;
    font-size:13px;
    margin-top:7px;
}

.card{
    background:rgba(255,255,255,.07);
    border:1px solid rgba(255,255,255,.12);
    border-radius:25px;
    padding:20px;
    box-shadow:0 20px 60px rgba(0,0,0,.45);
    backdrop-filter:blur(18px);
}

.heading{
    font-size:19px;
    font-weight:800;
    margin-bottom:7px;
}

.desc{
    color:#8496aa;
    font-size:12px;
    line-height:1.5;
    margin-bottom:18px;
}

.input{
    width:100%;
    height:58px;
    padding:0 16px;

    background:rgba(0,0,0,.30);
    border:1px solid rgba(255,255,255,.13);
    border-radius:16px;

    outline:none;
    color:#fff;
    font-size:15px;
}

.input::placeholder{
    color:#687b90;
}

.input:focus{
    border-color:#00d9ff;
    box-shadow:0 0 0 3px rgba(0,217,255,.08);
}

.generate{
    width:100%;
    height:55px;
    margin-top:14px;

    border:0;
    border-radius:16px;

    color:#fff;
    font-size:15px;
    font-weight:800;

    background:linear-gradient(135deg,#00d9ff,#006cff);

    box-shadow:0 10px 25px rgba(0,150,255,.25);
}

.generate:active{
    transform:scale(.98);
}

.message{
    display:none;
    margin-top:12px;
    padding:11px;
    border-radius:12px;
    background:rgba(255,70,70,.12);
    color:#ff9b9b;
    font-size:12px;
    text-align:center;
}

.result{
    display:none;
    margin-top:20px;

    background:#fff;
    border-radius:23px;
    padding:20px;

    text-align:center;
}

.result-title{
    color:#111827;
    font-size:16px;
    font-weight:800;
    margin-bottom:16px;
}

#qrcode{
    width:100%;
    display:flex;
    justify-content:center;
    align-items:center;
}

#qrcode canvas{
    display:block;
    width:260px !important;
    height:260px !important;
}

.link-box{
    margin-top:15px;
    padding:11px;

    border-radius:12px;
    background:#f1f5f9;

    color:#334155;
    font-size:11px;

    word-break:break-all;
}

.download{
    width:100%;
    height:52px;
    margin-top:15px;

    border:0;
    border-radius:15px;

    background:#111827;
    color:#fff;

    font-size:15px;
    font-weight:800;
}

.clear{
    width:100%;
    height:50px;
    margin-top:10px;

    border:0;
    border-radius:15px;

    background:rgba(255,255,255,.07);
    color:#cbd5e1;

    font-size:14px;
    font-weight:700;
}

.footer{
    text-align:center;
    color:#62748a;
    font-size:11px;
    margin-top:20px;
}
</style>
</head>

<body>

<div class="container">

    <div class="header">

        <div class="logo">
            Shirso <span>QR</span>
        </div>

        <div class="subtitle">
            Simple • Fast • Real QR Generator
        </div>

    </div>


    <div class="card">

        <div class="heading">
            Create QR Code
        </div>

        <div class="desc">
            Paste your link or number below. Your exact content
            will be encoded into the QR code.
        </div>


        <input
            id="input"
            class="input"
            type="text"
            placeholder="Paste your link or number"
            autocomplete="off"
            autocapitalize="off"
            spellcheck="false"
        >


        <button
            class="generate"
            onclick="makeQR()"
        >
            ⚡ Generate QR Code
        </button>


        <div
            id="message"
            class="message">
        </div>


        <div
            id="result"
            class="result">

            <div class="result-title">
                ✓ QR Code Ready
            </div>


            <div id="qrcode"></div>


            <div
                id="linkBox"
                class="link-box">
            </div>


            <button
                class="download"
                onclick="downloadQR()">
                ⬇ Download QR Code
            </button>

        </div>


        <button
            class="clear"
            onclick="clearAll()">
            Clear
        </button>

    </div>


    <div class="footer">
        Shirso QR Generator
    </div>

</div>


<script>

/*
====================================================
URL / TEXT PROCESSING
====================================================
*/

function prepareData(value){

    value = value.trim();

    /*
    If it already has a valid protocol,
    keep it exactly as entered.
    */

    if(
        value.startsWith("https://") ||
        value.startsWith("http://") ||
        value.startsWith("tel:") ||
        value.startsWith("mailto:")
    ){
        return value;
    }


    /*
    If it looks like a website,
    automatically add HTTPS.
    */

    if(
        value.startsWith("www.") ||
        value.includes(".com") ||
        value.includes(".net") ||
        value.includes(".org") ||
        value.includes(".bd") ||
        value.includes(".io") ||
        value.includes(".me") ||
        value.includes(".co")
    ){
        return "https://" + value;
    }


    /*
    Otherwise keep it as normal text/number.
    */

    return value;
}


/*
====================================================
GENERATE QR
====================================================
*/

function makeQR(){

    const input =
        document.getElementById("input");

    const result =
        document.getElementById("result");

    const qr =
        document.getElementById("qrcode");

    const message =
        document.getElementById("message");

    const linkBox =
        document.getElementById("linkBox");


    let value =
        input.value.trim();


    /*
    Empty input
    */

    if(value === ""){

        message.style.display="block";

        message.textContent =
        "Please paste your link or number first.";

        input.focus();

        return;
    }


    message.style.display="none";


    /*
    Convert website into a real URL.
    */

    const finalData =
        prepareData(value);


    /*
    IMPORTANT:
    Delete the previous QR completely.
    */

    qr.innerHTML="";


    /*
    Generate a NEW QR using the user's
    actual input as the QR DATA.
    */

    new QRCode(
        qr,
        {
            text:finalData,

            width:520,
            height:520,

            colorDark:"#000000",
            colorLight:"#ffffff",

            correctLevel:
            QRCode.CorrectLevel.H
        }
    );


    /*
    Show the exact data encoded.
    */

    linkBox.textContent =
        finalData;


    /*
    Show result.
    */

    result.style.display="block";


    /*
    Scroll to QR.
    */

    setTimeout(function(){

        result.scrollIntoView({
            behavior:"smooth",
            block:"center"
        });

    },100);

}


/*
====================================================
DOWNLOAD
====================================================
*/

function downloadQR(){

    const canvas =
        document.querySelector("#qrcode canvas");


    if(!canvas){

        alert("Please generate a QR code first.");

        return;
    }


    /*
    Create a high-quality PNG.
    */

    const downloadCanvas =
        document.createElement("canvas");

    const size = 1040;

    downloadCanvas.width=size;
    downloadCanvas.height=size;


    const ctx =
        downloadCanvas.getContext("2d");


    /*
    White background.
    */

    ctx.fillStyle="#ffffff";

    ctx.fillRect(
        0,
        0,
        size,
        size
    );


    /*
    Draw QR at high resolution.
    */

    ctx.imageSmoothingEnabled=false;

    ctx.drawImage(
        canvas,
        0,
        0,
        size,
        size
    );


    /*
    Download.
    */

    const link =
        document.createElement("a");

    link.download =
        "Shirso-QR-Code.png";

    link.href =
        downloadCanvas.toDataURL(
            "image/png"
        );

    document.body.appendChild(link);

    link.click();

    document.body.removeChild(link);

}


/*
====================================================
CLEAR
====================================================
*/

function clearAll(){

    document.getElementById("input").value="";

    document.getElementById("qrcode").innerHTML="";

    document.getElementById("linkBox").textContent="";

    document.getElementById("result").style.display="none";

    document.getElementById("message").style.display="none";

    document.getElementById("input").focus();

}

</script>

</body>
</html>
