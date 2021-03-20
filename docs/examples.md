---
hide:
    - navigation
title: Examples
description: View code examples for Brio Lang.
---
# Brio Lang Examples

This page contains a few code examples to demonstrate what Brio Lang can do!

## Send an SMS via Twilio
```brio
# Example: send a text message via Twilio

import env

class Twilio{
    method init(){
        # set common instance variables 
        @baseUrl = "https://api.twilio.com/2010-04-01/Accounts"
        @accountSid = env.get("AccountSid")
        @authToken = env.get("AuthToken")
        @from_ = env.get("From")
    }

    method sendText(number, message){
        # invoke the Twilio API to send a sms text
        let url = @baseUrl + "/" + @accountSid + "/Messages.json"
        let data = "From=" + @from_ + "&Body=" + message + "&To=" + number
        httpPost(url, @accountSid, @authToken, data);
    }
}

method main(){
    # instantiate Twilio class and invoke method
    let client = new Twilio()
    client.sendText("+16501112222", "Hello world!")
    print("message was sent!")
}
```

## Web Application
Brio Lang can also be used to write server-side applications using the FastCGI protocol.

```brio
method main(environ){
    let headers = "Content-Type: text/html\r\n\r\n";
    let index = open("index.html", "r")  # <h1>Hello world</h1>
    let body = index.read();
    return headers + body;
};
```

```sh
$ spawn-fcgi -p 8000 -n -- ./bin/brio -fcgi ./path/to/server.brio
```

After running `spawn-fcgi`, you may now issue requests to your HTTP server `http://localhost` which will pass the request to your Brio Lang application. Please note that your HTTP server must be configured appropriately, for example NGINX would have the following definition:

```nginx
events {
  worker_connections 1024;
}

http {
  server {
    listen 80;
    server_name localhost;

    location / {
      fastcgi_pass   127.0.0.1:8000;

      fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
      fastcgi_param  SERVER_SOFTWARE    nginx;
      fastcgi_param  QUERY_STRING       $query_string;
      fastcgi_param  REQUEST_METHOD     $request_method;
      fastcgi_param  CONTENT_TYPE       $content_type;
      fastcgi_param  CONTENT_LENGTH     $content_length;
      fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
      fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;
      fastcgi_param  REQUEST_URI        $request_uri;
      fastcgi_param  DOCUMENT_URI       $document_uri;
      fastcgi_param  DOCUMENT_ROOT      $document_root;
      fastcgi_param  SERVER_PROTOCOL    $server_protocol;
      fastcgi_param  REMOTE_ADDR        $remote_addr;
      fastcgi_param  REMOTE_PORT        $remote_port;
      fastcgi_param  SERVER_ADDR        $server_addr;
      fastcgi_param  SERVER_PORT        $server_port;
      fastcgi_param  SERVER_NAME        $server_name;
    }
  }
}
```

In the future when a built-in `Socket` abstraction is introduced, an HTTP server can be run directly within Brio Lang without requiring a separate component.

## Reversing a String

```brio
method reverse(value){
    let newString = ""

    each (let c : value){
        newString = c + newString
    }

    return newString
}

method main(){
    let x = reverse("Brio Lang")
    print(x)
}
```
```sh
$ brio my_app.brio
gnaL oirB
```