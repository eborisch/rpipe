# rpipe
Tool for reading from stdin and writing to rclone storage.
usage: rpipe.py [-h] [-c CHUNKSIZE] [-b BLOCKSIZE] [-t TEMPDIR] [-r] [-j JOBS]
                [-n]
                destination

Provides pipe in to / out of rclone destination

positional arguments:
  destination

optional arguments:
  -h, --help            show this help message and exit
  -c CHUNKSIZE, --chunksize CHUNKSIZE
                        Chunk size for splitting transfer [8MB]
  -b BLOCKSIZE, --blocksize BLOCKSIZE
                        Block size for read/write [64KB]
  -t TEMPDIR, --tempdir TEMPDIR
                        Directory for storing temporary files
  -r, --replay          Write previous saved stream to stdout
  -j JOBS, --jobs JOBS  Number of simultaneous rclone jobs
  -n, --nocheck         Don't check md5 at end (eg. crypto store

Works by creating temporary files of size --chunksize in --tempdir, and
uploading those. By default runs two 'jobs', such that an upload can be
occuring while the next chunk is being built. As such, tempdir needs to
be able to hold two chunks. They are deleted and checksum-ed along the
way, and verified during retrieval.

Make sure that your destination doesn't exist (purge it first.) This will
likely be added as a default step on a future version.

Examples:
    <some source> | rpipe.py remote:some/empty/loc
    <some source> | rpipe.py --nocheck crypt:an/encrypted/loc
                    ^ As we can't check the md5s of the deposited files on an
                      encrypted store...
    rpipe.py --replay remote:some/empty/loc | <some sink>
    rpipe.py --replay --nocheck crypt:an/ecrypted/loc | <some sink>
