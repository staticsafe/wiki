# InspIRCd Wiki &raquo; Modules &raquo; m_spanningtree &raquo; UUIDs (Universally Unique User Identifiers)

Our implementation and definition of SID, ID, and UID closely matches TS6's. That is:

* **SID** - A servers unique ID. This is three characters long and must be in the form
`[0-9][A-Z0-9][A-Z0-9]`. InspIRCd servers which are automatically generating their server IDs only
ever use server id's in the range 000-999, leaving server id's with letters as reserved for third
party servers such as services servers. This can help prevent collisions, as by default the SID is
automatically generated by InspIRCd whereas in TS6 it is admin-specified.

* ID - A clients unique ID. This is six characters long and must be in the form
`[A-Z][A-Z0-9][A-Z0-9][A-Z0-9][A-Z0-9][A-Z0-9]`. The numbers `[0-9]` at the beginning of an ID are
legal characters, but reserved for future use.

* UID or UUID - An ID concatenated to a SID. This forms the clients UID.

When allocating a UID, InspIRCd will 'increment' the value, e.g. the ID after 001AAAAAA is
001AAAAAB, which is followed by 001AAAAAC, etc etc. When the highest UID is reached, InspIRCd will
"wrap around", starting again at 001AAAAAA. Note that if the ID the server tries to allocate already
appears to be in use (possible after a wrap-around has occured) then the next ID in the sequence
will be tried repeatedly, until a free ID is found. This differs from TS6 implementations, some of
which will restart the server once the UID space has been exhausted.

### Server ID (SID) auto generation

InspIRCd servers have the capability to automatically generate a server ID. This is done using the
hash function for the GNU GCC hash_map against the server name and server GECOS. In pseudocode,
this can be represented as:

    SID := 0
    FOR EVERY CHARACTER IN SERVER_NAME:
    BEGIN
        SID := 5 * SID + VALUE_OF_CHARACTER_IN_SERVER_NAME
    END
    FOR EVERY CHARACTER IN SERVER_GECOS
    BEGIN
        SID := 5 * SID + VALUE_OF_CHARACTER_IN_SERVER_GECOS
    END
    SID := SID MODULUS 999

In the unlikely event that this automatically allocated ID conflicts with other IDs on the network,
an ID must be manually specified in the configuration file.