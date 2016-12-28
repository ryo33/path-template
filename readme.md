# @ryo33/path-template

Match and build paths from rails/sinatra-style url path templates.

## Installation

    npm install --save @ryo33/path-template

## Usage

    var PathTemplate = require("@ryo33/path-template");

### PathTemplate.parse(string)

Returns a new path template, generated from the given string.

    var template = PathTemplate.parse("/users/:userID");

### PathTemplate.inspect(template)

Returns the string representation of the given path template.

    PathTemplate.inspect(template);   // => "/users/:userID"

### PathTemplate.variables(template)

Returns an array of the variable names used in the given path template as
strings.

    var template = PathTemplate.parse("/*tree/photos/:name(.:format)");
    PathTemplate.variables(template);   // => ["tree", "name", "format"]

### PathTemplate.format(template, obj)

Returns a path as a string built by combining the template and the parameters in
obj

    PathTemplate.format(template, {userID: 1});   // => "/users/1"

### PathTemplate.match(template, string)

If string is a path matching the format of template, returns a match object
containing the parameters found in the path, otherwise returns `undefined`.

    PathTemplate.match(template, "/users/1");    // => {userID: "1"}
    PathTemplate.match(template, "/photos/1");   // => undefined

### PathTemplate.add(template, string)

Returns a new path template by appending the path described by string.

    var userPhoto = PathTemplate.add(template, "/photos/:photoID");
    PathTemplate.inspect(userPhoto);   // => "/users/:userID/photos/:photoID"

### PathTemplate.without(template, string)

Returns a new path without the path segments described by string.

    var template = PathTemplate.parse("/groups/:groupID/users/:userID/photos"),
        groupPhotos = PathTemplate.without(template, "/users/:userID");
    
    PathTemplate.inspect(groupPhotos);   // => "/groups/:groupID/photos"

## Template syntax

The characters `:`, `*`, `(`, and `)` have special meanings.

`:` indicates the following segment is the name of a variable
`*` indicates the following segment is the splat/glob
`(` starts an optional segment
`)` ends an optional segment

additionally `/` and `.` will start a new segment.

### Static Segments

    "/foo/bar.baz"
     ^   ^   ^
     |   |   Starts a segment, matching ".baz"
     |   |
     |   Starts a segment, matching "/bar"
     |
     Starts a segment, matching "/foo"

### Variables

    "/foo/:bar.baz"
     ^    ^   ^
     |    |   Starts a new segment, that matches ".baz"
     |    |
     |    Matches anything up to the start of the next segment, with the value
     |    being stored in the "bar" parameter of the returned match object
     |
     Starts a segment, matching "/foo"

### Splat/Glob

    "/foo/*bar"
     ^    ^
     |    Matches any number of segments, the values being stored as an array
     |    in the "bar" parameter of the returned match object
     |
     Starts a segment, matching "/foo"

#### Anonymous Splat/Glob

    "/foo/*"
     ^    ^
     |    Matches any number of segments, the values will not appear in the
     |    returned match object
     |
     Starts a segment, matching "/foo"

### Optional Segments

    "/foo(/baz)/baz"
     ^   ^    ^^
     |   |    |Starts a new segment, that matches "/baz"
     |   |    |
     |   |    Ends the optional segment
     |   |
     |   Starts an optional segment, this segment need not be in the path being
     |   matched for the match to be successful
     |
     Starts a segment, matching "/foo"
