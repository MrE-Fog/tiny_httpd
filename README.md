
# Tiny_httpd [![build status](https://travis-ci.org/c-cube/tiny_httpd.svg?branch=master)](https://travis-ci.org/c-cube/tiny_httpd) 

Minimal HTTP server using good old threads and `Scanf` for routing.

Free from all forms of `ppx`, async monads, etc.

The basic echo server from `src/examples/echo.ml`:

```ocaml

module S = Tiny_httpd

let () =
  let server = S.create () in
  (* say hello *)
  S.add_path_handler ~meth:`GET server
    "/hello/%s@/" (fun _req name () -> S.Response.make_ok ("hello " ^name ^"!\n"));
  (* echo request *)
  S.add_path_handler server
    "/echo" (fun req () -> S.Response.make_ok (Format.asprintf "echo:@ %a@." S.Request.pp req));
  Printf.printf "listening on http://%s:%d\n%!" (S.addr server) (S.port server);
  match S.run server with
  | Ok () -> ()
  | Error e -> raise e
```

```sh
$ ./echo.sh &
listening on http://127.0.0.1:8080

# the path "hello/name" greets you.
$ curl -X GET http://localhost:8080/hello/quadrarotaphile
hello quadrarotaphile!

# the path "echo" just prints the request.
$ curl -X GET http://localhost:8080/echo --data "coucou lol" 
echo:
{meth=GET;
 headers=Host: localhost:8080
         User-Agent: curl/7.66.0
         Accept: */*
         Content-Length: 10
         Content-Type: application/x-www-form-urlencoded;
 path="/echo"; body="coucou lol"}

```

## `http_of_dir`

Similar to `python -m http.server`, a simple program `http_of_dir` is provided.
It serves files from the current directory.

```sh
$ http_of_dir . -p 8080 &
$ curl -X GET http://localhost:8080
...
some html
...

```


## Why?

Why not? If you just want a super basic local server (perhaps for exposing
data from a local demon, like Cups or Syncthing do), no need for a ton of
dependencies or high scalability libraries.

## Documentation

See https://c-cube.github.io/tiny_httpd

## License

MIT.


