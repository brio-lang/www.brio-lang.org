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

class Twilio{
    method init(){
        # set common instance variables 
        @baseUrl = "https://api.twilio.com/2010-04-01/Accounts"
        @accountSid = getEnv("AccountSid")
        @authToken = getEnv("AuthToken")
        @from_ = getEnv("From")
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

## JSON Parser

```brio
###
The type values of a JSON token.
###
class TokenType {
    let EOF = 0  # special token for end of file
    let L_BRACE = 1  # {
    let R_BRACE = 2  # }
    let L_BRACKET = 3  # [
    let R_BRACKET = 4  # ]
    let COLON = 5  # :
    let COMMA = 6  # ,
    let LITERAL_STR = 7  # "foo"
    let LITERAL_INT = 8  # 50
    let LITERAL_DECIMAL = 9  # 3.14
    let LITERAL_BOOL = 10  # true
}

###
Class to represent a JSON token.
###
class Token {
    method init(type, text){
        @type = type
        @text = text
    }
}

###
Special exception for JSON parsing errors.
###
class JsonException {}

###
Lexer that takes a string input and outputs an array of tokens.
###
class JsonLexer {
    method init(data){
        @data = data
        @index = 0
        @character = data[0]        
    }

    method nextToken(){
        # retrieve the next token from input
        while (@character != "EOF"){
            if (@character == " "){
                @whitespace()
            }elseif (@character == "{"){
                @consume()
                return new Token(TokenType.L_BRACE, "{")
            }elseif (@character == "}"){
                @consume()
                return new Token(TokenType.R_BRACE, "}")
            }elseif (@character == "["){
                @consume()
                return new Token(TokenType.L_BRACKET, "[")
            }elseif (@character == "]"){
                @consume()
                return new Token(TokenType.R_BRACKET, "]")
            }elseif (@character == ":"){
                @consume()
                return new Token(TokenType.COLON, ":")
            }elseif (@character == ","){
                @consume()
                return new Token(TokenType.COMMA, ",")
            }elseif (@character == '"'){
                return @str()
            }elseif (@isNumber()){
                return @number()
            }else{
                return @keyword()
            }
        }
        return new Token(TokenType.EOF, "")
    }

    method consume(){
        # advance the index by one and fetch the next character
        @index += 1
        if (@index >= @data.size()){
            @character = "EOF"
        }else{
            @character = @data[@index]
        }
    }

    method whitespace(){
        # match whitespace rules
        while (@character == " "){
            @consume()
        }
    }

    method keyword(){
        # match keyword rules
        let value = ""
        while (@character.isalpha()){
            value += @character
            @consume()
        }

        if (@isBool(value)){
            return new Token(TokenType.LITERAL_BOOL, value)
        }else{
            raise JsonException("Unexpected Keyword")
        }
    }

    method str(){
        # match string rules
        @consume()
        let value = ""
        while (@character != '"'){
            value += @character
            @consume()
        }
        @consume()
        return new Token(TokenType.LITERAL_STR, value)
    }

    method isBool(value){
        # determine if string is a boolean value
        if (value == "true"){
            return true
        }elseif (value == "false"){
            return true
        }
        return false
    }

    method isNumber(){
        # determine if current character is a number 
        if (@character.isdigit()){
            return true
        }
        return false
    }

    method isDecimal(value){
        # determine if provided value is a decimal
        each (let c : value){
            if (c == "."){
                return true
            }
        }
        return false
    }

    method number(){
        # match number rules
        let value = ""

        while(@isNumber() or @character == "."){
            value += @character
            @consume()
        }

        if (@isDecimal(value)){
            return new Token(TokenType.LITERAL_DECIMAL, value)
        }
        return new Token(TokenType.LITERAL_INT, value)
    }
}

###
Parser that takes tokens from the lexer and produces a Brio object.
###
class JsonParser {
    method init(lexer){
        @lexer = lexer
        @p = 0
        @tokens = []
    }

    method lt(i){
        # fetch lookahead token at specified index
        return @tokens[@p + i - 1]
    }

    method la(i){
        # fetch lookahead token type at specified index
        let token = @lt(i)
        return token.type
    }

    method consume(){
        # advance the token index by one
        @p += 1
    }

    method match(token_type){
        # consume token if it is the expected type
        if (@la(1) == token_type){
            @consume()
        }else {
            raise JsonException("Unexpected Token")
        }
    }

    method parse(){
        # fetch tokens from the lexer
        let valid = true
        while (valid){
            let token = @lexer.nextToken()
            if (token.type == TokenType.EOF){
                valid = false
            }else{
                @tokens.push(token)
            }
        }

        # parse JSON object or array
        if (@la(1) == TokenType.L_BRACE){
            return @object()
        }elseif (@la(1) == TokenType.L_BRACKET){
            return @list()
        }else{
            raise JsonException("Invalid JSON")
        }
    }

    method object(){
        # match object rules
        let result = {}

        @match(TokenType.L_BRACE)

        while(@la(1) != TokenType.R_BRACE){
            let key = @id()
            @match(TokenType.COLON)
            let value = @term()
            
            result[key] = value

            if (@la(1) == TokenType.COMMA){
                @match(TokenType.COMMA)
            }
        }

        @match(TokenType.R_BRACE)
        return result
    }

    method list(){
        # match list rules
        let result = []

        @match(TokenType.L_BRACKET)

        while(@la(1) != TokenType.R_BRACKET){
            let value = @term()
            result.push(value)

            if (@la(1) == TokenType.COMMA){
                @match(TokenType.COMMA)
            }
        }

        @match(TokenType.R_BRACKET)
        return result
    }

    method id(){
        # match id rules
        if (@la(1) == TokenType.LITERAL_STR){
            return @str()
        }elseif (@la(1) == TokenType.LITERAL_DECIMAL){
            return @dec()
        }elseif (@la(1) == TokenType.LITERAL_INT){
            return @int()
        }
        raise JsonException("Invalid JSON")
    }

    method term(){
        # match term rules
        if (@la(1) == TokenType.L_BRACE){
            return @object()
        }elseif (@la(1) == TokenType.L_BRACKET){
            return @list()
        }elseif (@la(1) == TokenType.LITERAL_BOOL){
            return @bool()
        }else{
            return @id()
        }
    }

    method str(){
        # match str rules
        let token = @lt(1)
        @match(TokenType.LITERAL_STR)
        return token.text
    }

    method int(){
        # match int rules
        let token = @lt(1)
        @match(TokenType.LITERAL_INT)
        return integer(token.text)
    }

    method dec(){
        # match decimal rules
        let token = @lt(1)
        @match(TokenType.LITERAL_DECIMAL)
        return decimal(token.text)
    }

    method bool(){
        # match boolean rules
        let token = @lt(1)
        @match(TokenType.LITERAL_BOOL)
        if (token.text == "true"){
            return true
        }else{
            return false
        }
    }
}

###
Parses a string into a JSON object (dictionary or array).
###
method parse(text){
    let lexer = new JsonLexer(text)
    let parser = new JsonParser(lexer)
    return parser.parse()
}

###
Stringifies a JSON object (dictionary or array).
###
method dump(data){
    return string(data)
}
```