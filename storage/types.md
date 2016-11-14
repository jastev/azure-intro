Blobs, Tables, Queues, and Files
================================
Storage comes in four basic types: Blobs, Tables, Queues, and Files. 
Roughly speaking, these correspond to unstructured data, key/value data,
ordered messages, and SMB filestores.

It's helpful to have an overview of these types before designing your
storage solution, because many high-level design decisions will impact
low-level functionality in ways that are difficult to describe otherwise.

## Blobs
Blobs are simply unstructured binary data.  They come in three different
subtypes:  block blobs, append blobs, and page blobs.  Block and append
blobs are similar, in that they are composed of blocks of up to 4Mb in size,
and are ideal for storing files.  Block blobs are optimized for sequential
reads, and append blobs are optimized for trailing writes.

Page blobs are composed of 512-byte pages, and are optimized for 
random read/write operations.  All virtual hard disks (vhds) in Azure are
constructed with page blobs.

Blobs are stored within structures called containers.  A container can
hold blobs of any type.

## Tables
Tables are collections of entities stored by unique key, where each entity
is a collection of up to 252 name/value pairs (properties) totalling up to
1Mb in size.  Like other NoSQL data stores, tables do not enforce a schema
on their entities, are not relational, and are optimized for retreiving entities
by key.

Note that Storage Tables are similar to, but more limited than, Azure
DocumentDb.

## Queues
Queues are FIFO sequences of messages, which can be up to 64Kb each.  A
queue can have any number of readers and writers, but each message is
intended to be processed by a single reader.

Note that Storage Queues are similar to, but more limited than, Azure
ServiceBus.

## Files
Fileshares are SMB-accessible filesystems.  Within a share you can create
arbitrary directory structures, and read and write files within them.
