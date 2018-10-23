
# Instructions:
## Getting Started
In this exercise, we will be using data from `rolling stone's top 500 albums`. We have this data contained in the `data.csv` file. We will be building out the following functions to answer questions and interact with this data.

> **remember:** reading from a csv file in python looks like the following:

```python 
import csv

    with open(file_name) as f:
        # we are using DictReader because we want our information to be in dictionary format.
        reader = csv.DictReader(f)
        # some more code
```

Once we have our `reader` reading our file as dictionaries, we want our data to be a list of dictionaries. So, we need to loop through our `reader` and create a list. *hint: list comprehension / for loops are your friend"

```python
# our data will look something like this once we have read it and turned it into a list of `OrderedDict`s
# don't worry, the ordered dicts look different but we can interact with them the same way we do normal dicts
[OrderedDict([('number', '1'), ('year', '1967'), ('album', "Sgt. Pepper's Lonely Hearts Club Band"), ('artist', 'The Beatles'), ('genre', 'Rock'), ('subgenre', 'Rock & Roll, Psychedelic Rock')]), OrderedDict([('number', '2'), ('year', '1966'), ('album', 'Pet Sounds'), ('artist', 'The Beach Boys'), ('genre', 'Rock'), ('subgenre', 'Pop Rock, Psychedelic Rock')]), OrderedDict([('number', '3'), ('year', '1966'), ('album', 'Revolver'), ('artist', 'The Beatles'), ('genre', 'Rock'), ('subgenre', 'Psychedelic Rock, Pop Rock')])]
```

After we have our data formated the way we want it, we can now begin working on defining our functions.

## Importing our data


```python
import csv

our_data = []
with open('./data.csv') as f:
    reader = csv.DictReader(f)
    for item in reader:
        our_data.append(item)
        
```


```python
our_data[0]
```




    OrderedDict([('number', '1'),
                 ('year', '1967'),
                 ('album', "Sgt. Pepper's Lonely Hearts Club Band"),
                 ('artist', 'The Beatles'),
                 ('genre', 'Rock'),
                 ('subgenre', 'Rock & Roll, Psychedelic Rock')])



## Functions to build-out
### Searching Functions

* Find by name - Takes in a string that represents the name of an album. Should return a dictionary with the correct album, or return None.


```python
def find_by_name(our_data,name):
    for album in our_data:
        if album['album'] == name:
            return album
    return None
```

* Find by rank - Takes in a number that represents the rank in the list of top albums and returns the album with that rank. If there is no album with that rank, it returns None.


```python
def find_by_rank(our_data,rank):
    for album in our_data:
        if album['number'] == str(rank):
            return album
    return None
    
```

* Find by year - Takes in a number for the year in which an album was released and returns a list of albums that were released in that year. If there are no albums released in the given year, it returns an empty list.


```python
def find_by_year(our_data,year):
    return [album for album in our_data if album['number'] == str(year)]
```

* Find by years - Takes in a start year and end year. Returns a list of all albums that were released on or between the start and end years. If no albums are found for those years, then an empty list is returned.


```python
def multiple_years(our_data, start, end):
    count = start
    album_list = []
    while count <= end:
        album_list.append(find_by_year(our_data,count))
        count += 1
```

* Find by ranks - Takes in a start rank and end rank. Returns a list of albums that are ranked between the start and end ranks. If no albums are found for those ranks, then an empty list is returned.


```python
def multiple_ranks(our_data,start, end):
    count = start
    album_list = []
    while count <= end:
        album_list.append(find_by_rank(count))
        count += 1
```

## All Functions

* All titles - Returns a list of titles for each album.


```python
def all_titles(our_data):
    return [album['album'] for album in our_data]
```

* All artists - Returns a list of artist names for each album.


```python
def all_artists(our_data):
    return [album['artist'] for album in our_data]
```

## Questions to answer/functions

* Artists with the most albums - Returns the artist with the highest amount of albums on the list of top albums


```python
def most_popular_artist(our_data):
    counter_dict = {}
    for artist in all_artists(our_data):
        if artist in counter_dict:
            counter_dict[artist] += 1
        else:
            counter_dict[artist] = 1
    maximum_albums = max(counter_dict.values())
    artist_lists = []
    for keys, values in counter_dict.items():
        if values == maximum_albums:
            artist_lists.append(keys) 
    return artist_lists
        
```


```python
most_popular_artist(our_data)
```




    ['The Beatles', 'Bob Dylan', 'The Rolling Stones']



* Histogram of albums by decade - Returns a histogram with each decade pointing to the number of albums released during that decade.


```python
def histogram_decades(our_data):
    decade_dict = {}
    for album in our_data:
        decade = int(album['year'])//10
        if decade in decade_dict:
            decade_dict[decade] += 1
        else:
            decade_dict[decade] = 1
    return decade_dict
```


```python
# !pip install plotly
from plotly.offline import init_notebook_mode, plot, iplot
import plotly.graph_objs as go
hist_decades = histogram_decades(our_data)


init_notebook_mode(connected=True)
iplot([{'type':'bar','x' : ['1950s','1960s','1970s','1980s','1990s','2000s','2010s'],'y': list(hist_decades.values())}])

```


<script>requirejs.config({paths: { 'plotly': ['https://cdn.plot.ly/plotly-latest.min']},});if(!window.Plotly) {{require(['plotly'],function(plotly) {window.Plotly=plotly;});}}</script>



<div id="299a2414-85f8-46f9-a67d-3cbc5ff24905" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("299a2414-85f8-46f9-a67d-3cbc5ff24905", [{"x": ["1950s", "1960s", "1970s", "1980s", "1990s", "2000s", "2010s"], "y": [99, 181, 9, 65, 83, 39, 2], "type": "bar", "uid": "be3be186-d6ca-11e8-ac65-88e9fe61eb3a"}], {}, {"showLink": true, "linkText": "Export to plot.ly"})});</script>


* Histogram by genre - Returns a histogram with each genre pointing to the number of albums that are categorized as being in that genre.


```python
def histogram_genres(our_data):
    genre_list = []
    for album in our_data:
        genre_list.extend(genre.strip() for genre in album['genre'].split(','))
    genre_dict = {}
    for genre in genre_list:
        if genre in genre_dict:
            genre_dict[genre] += 1
        else:
            genre_dict[genre] = 1
    return genre_dict
```


```python
genre_hist = histogram_genres(our_data)
print(genre_hist)
```

    {'Rock': 339, 'Pop': 51, 'Funk / Soul': 76, 'Blues': 33, 'Jazz': 18, 'Folk': 32, 'World': 32, '& Country': 32, 'Classical': 1, 'Stage & Screen': 8, 'Reggae': 12, 'Hip Hop': 37, 'Electronic': 42, 'Latin': 5}



```python
iplot([{'type':'bar','x':list(genre_hist.keys()),'y':list(genre_hist.values()),'name':'Number of albums by genre'}])
```


<div id="8d6b1cda-99e0-4fad-b407-da96cb5d5437" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("8d6b1cda-99e0-4fad-b407-da96cb5d5437", [{"name": "Number of albums by genre", "x": ["Rock", "Pop", "Funk / Soul", "Blues", "Jazz", "Folk", "World", "& Country", "Classical", "Stage & Screen", "Reggae", "Hip Hop", "Electronic", "Latin"], "y": [339, 51, 76, 33, 18, 32, 32, 32, 1, 8, 12, 37, 42, 5], "type": "bar", "uid": "cd24e736-d6ca-11e8-b0ad-88e9fe61eb3a"}], {}, {"showLink": true, "linkText": "Export to plot.ly"})});</script>


## Next Steps

Now that we have our functions querying our Album data, let's compare that data with the top 500 songs. We have another file -- a text file! -- that contains the data we need for the top 500 songs. Reading a text file is pretty similar to reading a csv file, however, it tends to need a bit more massaging to get your data formatted the way you want it.

```python
    # open the text file in read
    text_file = open('top-500-songs.txt', 'r')
    # read each line of the text file
    # here is where you can print out the lines to your terminal and get an idea 
    # for how you might think about re-formatting the data
    lines = text_file.readlines()

    print(lines)
    # the output will look something like this:
['1\tLike a Rolling Stone\tBob Dylan\t1965\n', '2\tSatisfaction\tThe Rolling Stones\t1965\n', '3\tImagine\tJohn Lennon\t1971\n', "4\tWhat's Going On\tMarvin Gaye\t1971\n", '5\tRespect\tAretha Franklin\t1967\n', '6\tGood Vibrations\tThe Beach Boys\t1966\n', '7\tJohnny B. Goode\tChuck Berry\t1958\n', '8\tHey Jude\tThe Beatles\t1968\n', ...]
```

It looks like `\t` is how the text file is separating each element on a line. So, we need a function that can separate a string into a list. Then we can tell this function on what to split our string (i.e. `\t`). From there we will have a list of lists that are formatted like the following:
```python
["RANK", "NAME", "ARTIST", "YEAR"]
```
We need to use our knowledge of iterating to go through each of these elements and turn them into dictionaries with the keys, "rank", "name", "artist", "year", pointing to the appropriate values. So, for song number 1, we want it to look like:
```python
{'rank': 1, 'name': "Like a Rolling Stone", 'artist'"Bob Dylan": , 'year': 1965}
```

Once we have a list of songs that are formatted like the above, we can move on to figuring out which songs are from the top albums and which albums and artists have the most 'top songs'.


```python
text_file = open('top-500-songs.txt', 'r')
lines = text_file.readlines()

def file_splitter(splitting_device):
    return [line.strip().split(splitting_device) for line in lines]
```


```python
songs = file_splitter('\t')
```


```python
songs[:10]
```




    [['1', 'Like a Rolling Stone', 'Bob Dylan', '1965'],
     ['2', 'Satisfaction', 'The Rolling Stones', '1965'],
     ['3', 'Imagine', 'John Lennon', '1971'],
     ['4', "What's Going On", 'Marvin Gaye', '1971'],
     ['5', 'Respect', 'Aretha Franklin', '1967'],
     ['6', 'Good Vibrations', 'The Beach Boys', '1966'],
     ['7', 'Johnny B. Goode', 'Chuck Berry', '1958'],
     ['8', 'Hey Jude', 'The Beatles', '1968'],
     ['9', 'Smells Like Teen Spirit', 'Nirvana', '1991'],
     ['10', "What'd I Say", 'Ray Charles', '1959']]




```python
def song_dict_maker(songs):
    output_list = []
    for song in songs:
        new_dict = {}
        new_dict['rank'] = song[0]
        new_dict['song'] = song[1]
        new_dict['artist'] = song[2]
        new_dict['year'] = song[3]
        output_list.append(new_dict)
    return output_list
    
```


```python
song_dict = song_dict_maker(songs)
```


```python
import json

file = open('track_data.json', 'r')
json_data = json.load(file)

# print(json_data)
# output will look like this:
# [{'artist': 'The Beatles', 'album': "Sgt. Pepper's Lonely Hearts Club Band", 'tracks': ["Sgt. Pepper's Lonely Hearts Club Band - Remix", 'With A Little Help From My Friends - Remix', 'Lucy In The Sky With Diamonds - Remix', 'Getting Better - Remix', 'Fixing A Hole - Remix', "She's Leaving Home - Remix", 'Being For The Benefit Of Mr. Kite! - Remix', 'Within You Without You - Remix', "When I'm Sixty-Four - Remix", 'Lovely Rita - Remix', 'Good Morning Good Morning - Remix', "Sgt. Pepper's Lonely Hearts Club Band (Reprise) - Remix", 'A Day In The Life - Remix', "Sgt. Pepper's Lonely Hearts Club Band - Take 9 And Speech", 'With A Little Help From My Friends - Take 1 / False Start And Take 2 / Instrumental', 'Lucy In The Sky With Diamonds - Take 1', 'Getting Better - Take 1 / Instrumental And Speech At The End', 'Fixing A Hole - Speech And Take 3', "She's Leaving Home - Take 1 / Instrumental", 'Being For The Benefit Of Mr. Kite! - Take 4', 'Within You Without You - Take 1 / Indian Instruments', "When I'm Sixty-Four - Take 2", 'Lovely Rita - Speech And Take 9', 'Good Morning Good Morning - Take 8', "Sgt. Pepper's Lonely Hearts Club Band (Reprise) - Speech And Take 8", 'A Day In The Life - Take 1 With Hums', 'Strawberry Fields Forever - Take 7', 'Strawberry Fields Forever - Take 26', 'Strawberry Fields Forever - Stereo Mix 2015', 'Penny Lane - Take 6 / Instrumental', 'Penny Lane - Stereo Mix 2017']}, {'artist': 'The Beach Boys', 'album': 'Pet Sounds', 'tracks': ["Wouldn't It Be Nice - Digitally Remastered 96", 'You Still Believe In Me - Digitally Remastered 96', "That's Not Me - 1996 Digital Remaster", "Don't Talk (Put Your Head On My Shoulder) - 1996 Digital Remaster", "I'm Waiting For The Day - Digitally Remastered 96", "Let's Go Away For Awhile - Digitally Remastered 96", 'Sloop John B - 1996 - Remaster', 'God Only Knows - 1997 - Remaster', "I Know There's An Answer - Digitally Remastered 96", 'Here Today - Digitally Remastered 96', "I Just Wasn't Made For These Times - Digitally Remastered 96", 'Pet Sounds - Digitally Remastered 96', 'Caroline, No - 1996 Digital Remaster', 'Hang On To Your Ego', "Wouldn't It Be Nice - 2000 - Remaster", 'You Still Believe In Me - 1996 Digital Remaster', "That's Not Me - 1996 Digital Remaster", "Don't Talk (Put Your Head On My Shoulder) - 1996 Digital Remaster", "I'm Waiting For The Day - 1996 Digital Remaster", "Let's Go Away For Awhile - 1996 Digital Remaster", 'Sloop John B - 1996 Digital Remaster', 'God Only Knows - 1996 Digital Remaster', "I Know There's An Answer - 1996 Digital Remaster", 'Here Today - 1996 Digital Remaster', "I Just Wasn't Made For These Times - 1996 Digital Remaster", 'Pet Sounds - 1996 Digital Remaster', 'Caroline, No - 1996 Digital Remaster']}, {'artist': 'The Beatles', 'album': 'Revolver', 'tracks': ['Taxman - Remastered', 'Eleanor Rigby - Remastered', "I'm Only Sleeping - Remastered", 'Love You To - Remastered', 'Here, There And Everywhere - Remastered', 'Yellow Submarine - Remastered', 'She Said She Said - Remastered', 'Good Day Sunshine - Remastered', 'And Your Bird Can Sing - Remastered', 'For No One - Remastered', 'Doctor Robert - Remastered', 'I Want To Tell You - Remastered', 'Got To Get You Into My Life - Remastered', 'Tomorrow Never Knows - Remastered']}, {'artist': 'Bob Dylan', 'album': 'Highway 61 Revisited', 'tracks': ['Like a Rolling Stone', 'Tombstone Blues', 'It Takes a Lot to Laugh, It Takes a Train to Cry', 'From a Buick 6', 'Ballad of a Thin Man', 'Queen Jane Approximately', 'Highway 61 Revisited', "Just Like Tom Thumb's Blues", 'Desolation Row']}, ...]
```


```python
def track_cleaner(data):
    copy_data = data.copy()
    for idx, album in enumerate(data):
        copy_data[idx]['tracks'] = list({track.split('-')[0].strip() for track in album['tracks']})
    return copy_data
tracks = track_cleaner(json_data)
```

**albumWithMostTopSongs** - returns the name of the artist and album that has that most songs featured on the top 500 songs list


```python
def album_with_most_top_songs(song_dict,album_song_file):
    album_popularity_dict = {}
    for song in song_dict:
        for album in album_song_file:
            if song['song'] in album['tracks']:
                if album['album'] in album_popularity_dict:
                    album_popularity_dict[album['album']] += 1
                else:
                    album_popularity_dict[album['album']] = 1
#     print(album_popularity_dict)
    print(album_popularity_dict)
    most_pop_album_num = max(album_popularity_dict.values())
    
#     for keys, values in album_popularity_dict.items():
#         if values == most_pop_album_num:
            
    return [keys for keys, values in album_popularity_dict.items() if values == most_pop_album_num]
            
```


```python
album_with_most_top_songs(song_dict,tracks)
```

    {'Highway 61 Revisited': 3, "What's Going On": 1, 'Otis Blue: Otis Redding Sings Soul': 3, 'I Never Loved a Man the Way I Love You': 3, 'Ready to Die': 1, 'The Smile Sessions': 1, 'The Great Twenty Eight': 5, 'Meet The Beatles!': 2, 'The Sun Sessions': 2, 'Presenting the Fabulous Ronettes Featuring Veronica': 2, 'Live at Leeds': 3, 'My Generation': 2, 'Portrait of a Legend 1951-1964': 4, 'London Calling': 2, 'Are You Experienced': 4, 'Elvis Presley': 8, 'Let It Be': 2, 'Born to Run': 2, 'Rubber Soul': 2, 'Lady Soul': 2, 'The Anthology: 1961-1977': 1, 'Pet Sounds': 2, 'Layla and Other Assorted Love Songs': 3, 'Slowhand': 1, 'The Sun Records Collection': 6, 'Led Zeppelin': 2, '[Led Zeppelin IV]': 2, 'The Doors': 2, 'Achtung Baby': 2, 'Let It Bleed': 2, 'Music From Big Pink': 1, "Here's Little Richard": 2, 'Electric Ladyland': 2, 'John Wesley Harding': 1, 'Hotel California': 1, 'Purple Rain': 2, 'Thriller': 2, 'The Who Sell Out': 2, 'Talking Book': 1, 'Led Zeppelin II': 3, "Sgt. Pepper's Lonely Hearts Club Band": 2, 'The Paul Butterfield Blues Band': 1, 'Star Time': 1, 'All Time Greatest Hits': 1, 'Bringing It All Back Home': 4, 'Mr. Tambourine Man': 3, 'Van Halen': 1, 'Synchronicity': 1, 'If You Can Believe Your Eyes and Ears': 2, 'From Elvis in Memphis': 1, 'Ramones': 1, 'The Joshua Tree': 2, 'At Folsom Prison': 3, "I'm Still in Love With You": 1, 'Willy and the Poor Boys': 1, 'Suicide': 2, 'Bridge Over Troubled Water': 2, 'The "Chirping" Crickets': 2, '40 Greatest Hits': 2, 'Every Picture Tells a Story': 2, 'Fly Like an Eagle': 1, 'Rumours': 1, 'Squeezing Out Sparks': 1, 'Greatest Hits': 6, 'Odessey and Oracle': 1, 'Hunky Dory': 1, 'Moby Grape': 1, "Who's Next": 2, 'The Beatles ("The White Album")': 1, 'Revolver': 1, "There's a Riot Goin' On": 1, 'Please Please Me': 2, 'Physical Graffiti': 1, "The B 52's": 1, 'Pearl': 2, "A Hard Day's Night": 2, 'Loaded': 2, 'The Complete Recordings': 1, 'Down Every Road': 1, "I Do Not Want What I Haven't Got": 1, 'A Night at the Opera': 1, 'Tracy Chapman': 1, "Let's Get It On": 1, 'Berlin': 3, 'How Will the Wolf Survive?': 1, 'The Anthology': 2, 'Los Angeles': 1, 'Live in Cook County Jail': 1, 'Saturday Night Fever: The Original Movie Soundtrack Deluxe Edition': 2, "(pronounced 'leh-'nerd 'skin-'nerd)": 1, 'Appetite for Destruction': 2, 'Abbey Road': 3, 'Kick Out the Jams': 1, 'The Cars': 1, 'Fresh Cream': 1, 'Wheels of Fire': 3, "Howlin' Wolf": 1, 'Out of Our Heads': 1, 'Moondance': 1, 'Sweet Baby James': 1, 'Blonde on Blonde': 2, 'At Last!': 1, 'Paranoid': 2, 'All the Young Dudes': 2, 'Highway to Hell': 1, 'OK Computer': 1, 'Innervisions': 1, 'Going to a Go-Go': 1, 'Sail Away': 1, 'Rust Never Sleeps': 1, 'New York Dolls': 1, 'The Modern Lovers': 1, 'Volunteers': 1, 'Born in the U.S.A.': 1, 'The Rise and Fall of Ziggy Stardust and the Spiders From Mars': 1, 'Stardust': 1, 'Court and Spark': 1, 'Slanted and Enchanted': 1, 'Raising Hell': 2, 'Toys in the Attic': 3, 'The Marshall Mathers LP': 1, 'Beggars Banquet': 1, 'Red Headed Stranger': 1, 'Between the Buttons': 1, 'The Wall': 1, 'Wish You Were Here': 1, 'My Aim Is True': 1, 'The Birth of Soul': 1, "That's the Way of the World": 1, 'Sticky Fingers': 2, 'The Velvet Underground': 2, 'The Harder They Come': 2, 'Axis: Bold as Love': 1, 'Violator': 1, 'Marquee Moon': 1, 'The Bends': 1, 'Murmur': 1, 'Goodbye Yellow Brick Road': 1, 'At Fillmore East': 1, 'Paid in Full': 1, 'Tim': 1, "Outlandos d'Amour": 1, 'American Recordings': 1, '#1 Record': 1, 'Metallica ("The Black Album")': 1, 'Close to You': 1, 'Doolittle': 1, 'Crosby, Stills & Nash': 1, 'Exile on Main St.': 1, 'Forever Changes': 1, 'The Stooges': 1, 'MTV Unplugged in New York': 2, 'Funky Kingston': 1, 'The Velvet Underground & Nico': 1, 'Live at the Harlem Square Club, 1963': 1, 'All Things Must Pass': 1, 'In Utero': 1, 'Rocket to Russia': 1, 'Damn the Torpedoes': 1, 'My Beautiful Dark Twisted Fantasy': 1, 'Raw Power': 1, 'Tapestry': 1, 'Surrealistic Pillow': 1, 'Nightbirds': 1, 'Love It to Death': 1, 'Graceland': 1, 'Some Girls': 1, 'Weezer (Blue Album)': 1}





    ['Elvis Presley']



**albumsWithTopSongs** - returns a list with the name of only the albums that have tracks featured on the list of top 500 songs


```python
def album_with_top_songs(our_data,song_dict,album_song_file):
    albums_with_popular_songs = []
    for song in song_dict:
        for album in album_song_file:
            if song['song'] in album['tracks']:
                albums_with_popular_songs.append(album['album'])
                
    ultimate_album_list = []            
    for album in albums_with_popular_songs:
        for top_albums in all_titles(our_data):
            if album in top_albums:
                ultimate_album_list.append(album)
                
    return list(set(ultimate_album_list))
                
    
```


```python
len(album_with_top_songs(our_data,song_dict,tracks))
```




    168



**songsThatAreOnTopAlbums** - returns a list with the name of only the songs featured on the list of top albums


```python
def songs_on_top_albums(top_albums,album_song_file):
    set_titles = set(all_titles(top_albums))
    song_list = []
    for album in album_song_file:
        if album['album'] in set_titles:
            song_list.extend(album['tracks'])
    return song_list   
```


```python
len(songs_on_top_albums(our_data,tracks))
```




    6754



**top10AlbumsByTopSongs** - returns a histogram with the 10 albums that have the most songs that appear in the top songs list. The album names should point to the number of songs that appear on the top 500 songs list.


```python
def top_10_albums_by_songs(song_dict,album_song_file):
    album_popularity_dict = {}
    for song in song_dict:
        for album in album_song_file:
            if song['song'] in album['tracks']:
                if album['album'] in album_popularity_dict:
                    album_popularity_dict[album['album']] += 1
                else:
                    album_popularity_dict[album['album']] = 1
#     print(album_popularity_dict)
    sorted_by_value = sorted(album_popularity_dict.items(), key=lambda kv: kv[1],reverse=True)
    num_top_songs_on_album = {a:b for a,b in sorted_by_value[:10]}
    return iplot([{'type':'bar','x':list(num_top_songs_on_album.keys()),'y':list(num_top_songs_on_album.values())}])
      
    
```


```python
top_10_albums_by_songs(song_dict,tracks)
```


<div id="b3b06d01-8bec-4ab8-a761-a9bc65e673c0" style="height: 525px; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">require(["plotly"], function(Plotly) { window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("b3b06d01-8bec-4ab8-a761-a9bc65e673c0", [{"x": ["Elvis Presley", "The Sun Records Collection", "Greatest Hits", "The Great Twenty Eight", "Portrait of a Legend 1951-1964", "Are You Experienced", "Bringing It All Back Home", "Highway 61 Revisited", "Otis Blue: Otis Redding Sings Soul", "I Never Loved a Man the Way I Love You"], "y": [8, 6, 6, 5, 4, 4, 4, 3, 3, 3], "type": "bar", "uid": "f66d2f22-d6ca-11e8-9aa7-88e9fe61eb3a"}], {}, {"showLink": true, "linkText": "Export to plot.ly"})});</script>


**topOverallArtist** - Artist featured with the most songs and albums on the two lists. This means that if Brittany Spears had 3 of her albums featured on the top albums listed and 10 of her songs featured on the top songs, she would have a total of 13. The artist with the highest aggregate score would be the top overall artist.


```python
from collections import Counter
def top_overall_artist(album_data,song_data):
    artist_album_count = Counter(all_artists(album_data))
    artist_song_count = Counter(all_artists(song_data))
#     print(artist_song_count)
#     print(artist_album_count)
    for artist in artist_album_count:
        if artist in artist_song_count:
            artist_song_count[artist] += artist_album_count[artist]
        else:
            artist_song_count[artist] = artist_album_count[artist]
            
    maximum_agg = max(artist_song_count.values())
#     print(maximum_agg)
    lst = []
    for key, value in artist_song_count.items():
        if value == maximum_agg:
            lst.append(key)
    return lst
```


```python
top_overall_artist(our_data,song_dict)
```




    ['The Beatles']


