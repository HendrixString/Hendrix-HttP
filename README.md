# Hendrix-HttP
a lightweight `HTTP` library for `ActionScript 3` / `Adobe AIR` inspired by `Square's OkHttp` Builder patterns and flavours
for constructing a legit `HTTP` requests of all types.

## How to use
simply fork or download the project, you can also download the binary itself and link it
to your project, or import to your IDE of choice such as `Flash Builder 4.7`.

## Features
- supports all `HTTP` methods.
- supports MultiPart requests.
- includes Builders for:
  - `Request` builder.
  - `Form Encoding` body builder.
  - `Multipart` body builder.
  - `Mime` builder.
  - `Request Body` composer.
- receive `json` responses as classes (via `Vanilla` library).
- most important: quick and easy to use.

## Guide
All of the demonstrations below show implementations based on the `Google Glass Mirror RESTful API`.

###### 1) Simple `GET` request

```actionscript
public function request():void
{
    new RequestBuilder().GET()
                        .url(SConfig.HOST + "/mirror/v1/timeline/" + _id)
                        .addQuery("access_token", _oauthToken).responseClass(TimelineItem)
                        .build().execute(onComplete, onError);
}

private function onComplete(response: TimelineItem):void {

}

private function onError(response: Object):void {

}

```

###### 2) `Form Encoding POST` request

```actionscript
public function request():void {
    var body: RequestBody = new FormEncodingBuilder().add("access_token", _oauthToken)
                                                     .add("name", "value").build();
    
    new RequestBuilder().POST(body)
                        .url(SConfig.HOST + "/mirror/v1/timeline/" + _id)
                        .responseClass(TimelineItem)
                        .build().execute(onComplete, onError);
}

private function onComplete(response: TimelineItem):void {

}

private function onError(response: Object):void {

}
```

###### 3) `MultiPart` request: 
- conditional two parts request. 
- uploading data with optional image media file. 
- if image is `null` than construct a `POST` request with `json` body (string body).
- else construct a multipart request with two parts: a `json` part(string body), image part (`ByteArray` body).

```actionscript
public function request(item: TimelineItem, image: ByteArray = null):void {
      var json_timeline:  String        = JSON.stringify(item);
      
      var body:    RequestBody = null;
      var request: Request     = null;
    
      if(_image == null) {
        body       = RequestBody.create(json_timeline, "application/json");
        
        request    = new RequestBuilder().url(SConfig.HOST + "/mirror/v1/timeline")
                                         .addQuery("access_token", _oauthToken)
                                         .POST(body).responseClass(TimelineItem)
                                         .build().execute(onComplete, onError);
      }
      else {
        body       = new MultiPartBuilder().addPart(RequestBody.create(json_timeline, "Content-Type: application/json; charset=UTF-8"))
                                           .addPart(RequestBody.create(image, "Content-Type: image/jpeg"),"Content-Transfer-Encoding: binary")
                                           .type(MultiPartBuilder.RELATED).build();
  
        request    = new RequestBuilder().addHeader("Cache-Control", "no-cache")
                                         .url(SConfig.HOST + "/upload/mirror/v1/timeline").addQuery("access_token", _oauthToken)
                                         .addQuery("uploadType", _uploadType)
                                         .POST(body).responseClass(TimelineItem)
                                         .build().execute(onComplete, onError);
}

private function onComplete(response: TimelineItem):void {

}

private function onError(response: Object):void {

}

```

###### 4) `POST` image request

```actionscript
public function uploadImage(image: ByteArray):void {
      var body:    RequestBody = null;
      var request: Request     = null;
      
      body      = RequestBody.create(image, "image/png");
      
      request   = new RequestBuilder().url(SConfig.HOST + "/upload/mirror/v1/timeline" + "/" + _itemId + "/attachments")
                                      .addQuery("access_token", _oauthToken)
                                      .POST(body).responseClass(Attachment)
                                      .build().execute(onComplete, onError);
}

public function uploadImageFile(file: File):void {
      var body:    RequestBody = null;
      var request: Request     = null;
      
      body     = RequestBody.create(file, "image/png");
      
      request  = new RequestBuilder().url(SConfig.HOST + "/upload/mirror/v1/timeline" + "/" + _itemId + "/attachments")
                                     .addQuery("access_token", _oauthToken)
                                     .POST(body).responseClass(Attachment)
                                     .build().execute(onComplete, onError);
}

private function onComplete(response: Attachment):void {

}

private function onError(response: Object):void {

}
```

### Dependencies
* [`Vanilla library`](https://github.com/jonnyreeves/as3-vanilla)

### Terms
* completely free source code. [Apache License, Version 2.0.](http://www.apache.org/licenses/LICENSE-2.0)
* if you like it -> star or share it with others

### Contact Author
* [tomer.shalev@gmail.com](tomer.shalev@gmail.com)
* [Google+ TomershalevMan](https://plus.google.com/+TomershalevMan/about)
* [Facebook - HendrixString](https://www.facebook.com/HendrixString)
