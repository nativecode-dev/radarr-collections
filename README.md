# Radarr Collection Manager

A Python script for checking your [Radarr](https://radarr.video/) database against [TMDB](https://www.themoviedb.org/) Collections. <br>
This is my first experience writing code not to do with scientific data analysis so it may not be amazingly written.

This downloads information directly from the movie's TMDB page and TMDB Collections are strictly limited to sequels. For example, with [Dark Knight (2008)](https://www.themoviedb.org/movie/155-the-dark-knight) in my database, this will look at the attached [collection information](https://www.themoviedb.org/collection/263-the-dark-knight-collection?language=en-US), check the Radarr database for [Batman Begins (2005)](https://www.themoviedb.org/movie/272?language=en-US) and [The Dark Knight Rises (2012)](https://www.themoviedb.org/movie/49026?language=en-US) and then will automatically add any that are not in the database. If you already have two or more of the movies in a collection, it will only check the collection once. 

## Features: <br>
- Automatically Added into Radarr using API, <br>
- Option to add Monitored or Unmonitored, <br>
- Automatic Search (optional), <br>
- Ignore Wanted list. Only check movies with files (optional),<br>
- Outputs list of collection artwork URLs to text file. (optional)<br>

**Update** <br>
- Multiple root folder support <br>
- Multiple Profile Support <br>
	_New items added will use the same Profile and Root Path as the movie currently being checked_.<br>
- Ignore Wanted List<br>
	_Use flag_ `-d` _to only check movies with files_
- API error handling<br>
	_429 rate limiting and retry cycle before fatal error_
  
## Requirements:
- Radarr, <br>
- Your own TMDB API key, <br>
- Python modules: requests, json, datetime, os, sys, getopt, time
  
## Getting a TMDB API key:
TMDB offers free API keys to anyone with an account. Simply sign up for an account and request a key via your account settings. I did intend to embed a key into the code but couldn't work out how to hide it from public view so I'm afraid you'll need to get your own.
  
## config.py
### Radarr settings

All values need to be in ""<br>

- **Host and Port** <br>
If running Radarr in a Docker or on a different machine, the host will need to be set to the IP address of the (virtual) machine running Radarr. Please use the same values as you use for accessing the Web interface. Default for running on the same machine is `"localhost"` and `"7878"` <br>

- **base url** <br>
Used for reverse proxies. Should be set as `"off"` unless needed, if used needs to have / included eg. `"/radarr"`. <br>

- **api key** <br>
Can be found under Settings > General <br>

### Other Variables 
**tmdbkey** is where to paste your TMDB API key, also needs "".

**Monitored** and **Autosearch** are boolean (`True`/`False`) switches, need the capital first letter but do not need the "" <br>
The first time you run the script, it is reccomended to have both set to False. From a database of 1200 movies, this added 180 more on my first run and having it autosearch all of these is a bad idea. 

## Running
Run `python rcm.py [options]` to initiate a sync.<br>

#### Options
- `-h` or `--help`	&nbsp;&nbsp;&nbsp;	Displays this help 
- `-q` or `--quiet`	&nbsp;&nbsp;&nbsp;	Disable verbose logging in command line. Log file still created.
- `-f` or `--full`	&nbsp;&nbsp;&nbsp;	Run full scan, check all items.
- `-d` or `--down`	&nbsp;&nbsp;&nbsp;	Only search movies with files. Ignore Wanted list.
- `-a` or `--art`	&nbsp;&nbsp;&nbsp;	Output artwork URL file.
- `-s` or `--start`	&nbsp;&nbsp;&nbsp;	Specify start point, useful for fatal error restarts.

Every time the script is run it will save a list of all the TMDB IDs in your Radarr database at that moment.
- Passing the `-f` or `--full` option will not import this list and will run a full scan and recheck every item.
- **Not** passing the `-f` option will initiate an update scan, import the list and only check items added to Radarr since to save on unnecessary API calls to TMDB.

Multiple options can be passed in. `python rcm.py -d -q -f` would work for example.

## Output
As well as automatically adding the movies into Radarr there is an output folder with additional files. <br>
- added [date].txt is a secondary log file that lists the movies that have been added on that run. It is not created if 0 movies are added. <br>
- When running with the `-a` flag, art.txt is a list of every collection that has been checked along with the URL to the default collection artwork from TMDB to be easily pasted into Plex or other media apps. It won't be an active link in the output file but continuing with the Batman theme for examples, it's entry would be:
> The Dark Knight Collection 	&nbsp;&nbsp;&nbsp;&nbsp; 	https://image.tmdb.org/t/p/original/bqS2lMgGkuodIXtDILFWTSWDDpa.jpg
