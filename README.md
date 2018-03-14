# keymaker.js
magicurl generation and cookie authentication for managing unix identity and access control 

Built for MIXINT (mixin interface) - if it can be modified to be useful in other applications let's talk.

When invoked or required, keymaker.js boots a http server meant to be locked down for localhost usage only: 
POST requests can be made to create an identity, modify the groups an identity belongs to, and generate cookies and magicurls to log in as that unix identity.

`function makeYourselfAtHome` takes {login (name), groups, comment?, key?, create-home true? user-group true?} via post request or allow and returns a uid
`function makeMagicURL` takes a uid and a bytesize and a timeout and generates a one-time-use crytpographic string that can be used to get a key. The key will be persistent, and should be kept encrypted (I mean its on a cookie and not super-secret, but its your auth token attached to cookies, if its intercepts someone else can pretend to be you).
`function makeCookie` identifies who a magicurl was created for, if it hasn't expired, and generates a separate cyrptographic token to use as a persistent auth token.
`function identify` takes a token and returns the profile of the user it corresponds to, which may be modified directly, for instance when operator starts a switchboard it can save references to the process, pid, and port on this profile object to keep it handy whenever requested.

Utilizes promisified child_process execs to perform the process of checking if an identity exists on the machine and creates it or updates it by executing 'useradd' 'groupadd' and 'usermod' commands assumed to exist on underlying os (so bsd and linux should work great out of the box, darwin/osx needs to have custom groupadd useradd commands available on path)

Might prototype a server pattern here that doesn't require explicitly setting routes, but instead takes a class name and a function name in the path `/class/call?kwargs` and passing the kwargs as an object to the function... yea I like that. Or is json body better? I think query string is probably more succint code.

I think on boot the server will also save generate a cryptographic token that is private and/or prints to stdout, and this token will be required to command the server. It can be passed to another service that should be allowed to manage accounts, so just getting logged onto the system / getting access to port 8282 or whatever isn't enough to muck with users... or can I lock a port down so it's only accessible to members of a certain group.

await new keymaker -> token and functions are all available on object

so keymaker is a class with a constructor that boots the server and resolves ? sure constructor returns a promise why not...

