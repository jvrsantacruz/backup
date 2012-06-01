# backup.py

Make data snapshots local or remote (ssh/http/rsync) with no space overhead.

When it comes to backups, simplicity it's always welcome. You've got files within directories, so
store them exactly as they were. But transfer only those which change, and avoid replication.

`backup.py` just copies and stores changing files when performing a backup, and yet making it
appear as a complete full copy on destination. Very much like *TimeMachine* on MacOs.

# How to use it

If you were to save directory in your home folder to a external hard drive, you could call
the program straight from the command line like this:

	./backup  --origin ~/data  --dest /media/external   backup

But you're more likely to write a backup plan in a very simple `config.yaml` file and just make:

	./backup --plan config.yaml   backup

With your `config.yaml` looking like this:

	# backup plan
	dest: /media/external
	origins:
		- /home/user/data

But you can do much more than just this. For example, you could *pull* your data from a remote
location by simply writing one of your 'origins' in a way that would be recognized by `rsync`:

	# backup plan
	dest: /media/external
	origins:
		- user@machine:/home/user/data    # ssh
		- machine::module/home/user/data  # rsync (http)

# How a backup looks like?

That's the interesting part: It looks exactly as the original. If you make 5 copies, you got 5 full
copies on destination, but taking the space of 1 copy plus changes between them.

In other words, imagine you have this bunch of personal files that accounts for about 15 Megabytes.

	$ tree -h /home/user/data
	/home/user/data
	├── [15.0M]  desktop
	│   ├── [12M]  beethoven.mp3
	│   └── [ 3M]  booklog.txt
	└── [4.0K]   myfolder
		└── [  12]  shoplist.txt

		2 directories, 3 files

And then you perform a couple of copies using `backup.py`:
	
	$ ./backup -o /home/user/data -d /media/external/backups  backup
	$ ./backup -o /home/user/data -d /media/external/backups  backup

You'll end up with two time-stamped directories in `/media/externa/backups`, holding your files
inside exactly as they were:
