There are three ways of API Versioning
version can be put in v1/v2 in path.(used by stripe and nearly all apis)
or in accept header(used by stripe(stripe uses both) and github)
or in query parameter. 

For new verion but this is important.
when v2 ships , you need to send 3 response header in v1 so that client can know that it is deprecated. They are
Deprecation header, to know when v1 was deprecated.
Sunset, date it will stop working
link header will point to migration guide

depend on client whether the client is strict or not. 