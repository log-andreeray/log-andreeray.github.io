---
date: '2016-12-25 13:41 +0100'
published: true
title: Node -  File Uploader
category:
  - Node
---
**upload.html**

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <title>File Uploader - coligo.io</title>
  <link href='https://fonts.googleapis.com/css?family=Raleway' rel='stylesheet' type='text/css'>
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
  <link href="css/styles.css" rel="stylesheet">
</head>
<body>

  <div class="container">
    <div class="row">
      <div class="col-xs-12">
        <div class="panel panel-default">
          <div class="panel-body">
            <span class="glyphicon glyphicon-cloud-upload"></span>
            <h2>File Uploader</h2>
            <h4>coligo.io</h4>
            <div class="progress">
              <div class="progress-bar" role="progressbar"></div>
            </div>
            <button class="btn btn-lg upload-btn" type="button">Upload File</button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <input id="upload-input" type="file" name="uploads[]" multiple="multiple"></br>

  <script src="https://code.jquery.com/jquery-2.2.0.min.js"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js"></script>
  <script src="javascripts/upload.js"></script>
</body>
</html>
```

**upload.css**

```css
.btn:focus, .upload-btn:focus{
  outline: 0 !important;
}

html,
body {
  height: 100%;
  background-color: #4791D2;
}

body {
  text-align: center;
  font-family: 'Raleway', sans-serif;
}

.row {
  margin-top: 80px;
}

.upload-btn {
  color: #ffffff;
  background-color: #F89406;
  border: none;
}

.upload-btn:hover,
.upload-btn:focus,
.upload-btn:active,
.upload-btn.active {
  color: #ffffff;
  background-color: #FA8900;
  border: none;
}

h4 {
  padding-bottom: 30px;
  color: #B8BDC1;
}

.glyphicon {
  font-size: 5em;
  color: #9CA3A9;
}

h2 {
  margin-top: 15px;
  color: #68757E;
}

.panel {
  padding-top: 20px;
  padding-bottom: 20px;
}

#upload-input {
  display: none;
}

@media (min-width: 768px) {
  .main-container {
    width: 100%;
  }
}

@media (min-width: 992px) {
  .container {
    width: 450px;
  }
}
```

**upload.js**

```js
$('.upload-btn').on('click', function (){
    $('#upload-input').click();
    $('.progress-bar').text('0%');
    $('.progress-bar').width('0%');
});

$('#upload-input').on('change', function(){

  var files = $(this).get(0).files;

  if (files.length > 0){
    // create a FormData object which will be sent as the data payload in the
    // AJAX request
    var formData = new FormData();

    // loop through all the selected files and add them to the formData object
    for (var i = 0; i < files.length; i++) {
      var file = files[i];

      // add the files to formData object for the data payload
      formData.append('uploads[]', file, file.name);
    }

    $.ajax({
      url: '/upload',
      type: 'POST',
      data: formData,
      processData: false,
      contentType: false,
      success: function(data){
          console.log('upload successful!\n' + data);
      },
      xhr: function() {
        // create an XMLHttpRequest
        var xhr = new XMLHttpRequest();

        // listen to the 'progress' event
        xhr.upload.addEventListener('progress', function(evt) {

          if (evt.lengthComputable) {
            // calculate the percentage of upload completed
            var percentComplete = evt.loaded / evt.total;
            percentComplete = parseInt(percentComplete * 100);

            // update the Bootstrap progress bar with the new percentage
            $('.progress-bar').text(percentComplete + '%');
            $('.progress-bar').width(percentComplete + '%');

            // once the upload reaches 100%, set the progress bar text to done
            if (percentComplete === 100) {
              $('.progress-bar').html('Done');
            }

          }

        }, false);

        return xhr;
      }
    });

  }
});
```

**server.js**

```js
var express = require('express');
var app = express();
var path = require('path');
var formidable = require('formidable');
var fs = require('fs');

app.use(express.static(path.join(__dirname, 'public')));

app.get('/', function(req, res){
  res.sendFile(path.join(__dirname, 'views/index.html'));
});

app.post('/upload', function(req, res){

  // create an incoming form object
  var form = new formidable.IncomingForm();

  // specify that we want to allow the user to upload multiple files in a single request
  form.multiples = true;

  // store all uploads in the /uploads directory
  form.uploadDir = path.join(__dirname, '/uploads');

  // every time a file has been uploaded successfully,
  // rename it to it's orignal name
  form.on('file', function(field, file) {
    fs.rename(file.path, path.join(form.uploadDir, file.name));
  });

  // log any errors that occur
  form.on('error', function(err) {
    console.log('An error has occured: \n' + err);
  });

  // once all the files have been uploaded, send a response to the client
  form.on('end', function() {
    res.end('success');
  });

  // parse the incoming request containing the form data
  form.parse(req);

});

var server = app.listen(3000, function(){
  console.log('Server listening on port 3000');
});
```