create table if not exists Artists (
    id SERIAL primary key,
    name VARCHAR(50) not null,
    description TEXT
);

create table if not exists Genres (
    id SERIAL primary key,
    name VARCHAR(50) not null,
    description TEXT
);

create table if not exists ArtistGenres (
    artist_id INTEGER references Artists(id),
    genre_id INTEGER references Genres(id),
    constraint ag primary KEY(artist_id, genre_id)
);

create table if not exists Albums (
    id SERIAL primary key,
    name VARCHAR(50) not null,
    description TEXT,
    release_year DATE not NULL
);

create table if not exists ArtistAlbums (
    artist_id INTEGER references Artists(id),
    album_id INTEGER references Albums(id),
    constraint aa primary KEY(artist_id, album_id)
);

create table if not exists Tracks (
    id SERIAL primary key,
    album_id INTEGER references Albums(id),
    name VARCHAR(50) not null,
    description TEXT,
    song_duration INTEGER not null
); 

create table if not exists Collections (
    id SERIAL primary key,
    name VARCHAR(50) not null,
    release_year DATE not null
);

create table if not exists CollectionTracks (
    collection_id INTEGER references Collections(id),
    track_id INTEGER references Tracks(id),
    constraint ct primary KEY(collection_id, track_id)
);
INSERT INTO Artists (name, description) VALUES 
	('Metallica', 'Rock group'),
	('AC/DC', 'Rock group'),
	('Eminem', 'Rap artist'),
	('Taylor Swift', 'Pop artist');

INSERT INTO Genres (name, description) VALUES 
	('Rock', 'Rock'),
	('Rap', 'Rap'),
	('Pop', 'Pop');

INSERT INTO Albums (name, description, release_year) VALUES 
	('Music to Be Murdered By', 'Label: Shady, Aftermath, Interscope', '2019-01-01'),
	('Power Up', 'Formats: CD, LP, digital download, cassette, streaming', '2018-02-01'),
	('Lover', 'Formats: CD, LP, digital download, cassette, streaming', '2020-03-01'),
	('My albums', 'Label: Metallica, Aftermath, Interscope', '2019-01-01');

INSERT INTO Tracks (album_id, name, description, song_duration) VALUES 
	(1, 'You Gon Learn', 'featuring Royce da 5''9" and White Gold', '00:03:57'),
	(1, 'In Too Deep', 'MathersSubyOliver ChaninRestoSylvester Jordan', '00:03:25'),
	(2, 'Shot in the Dark', 'Third track', '00:03:05'),
	(2, 'Demon Fire', '7th Track', '00:03:30'),
	(3, 'The Man', 'The man', '00:03:11'),
	(3, 'my Lover', 'Lover', '00:03:32');

INSERT INTO Collections (name, release_year) VALUES 
	('Best Rap', '2018-04-01'),
	('Top Rock', '2019-05-01'),
	('Pop', '2020-06-01'),
	('Old Rock', '2024-07-01');

INSERT INTO ArtistGenres (artist_id, genre_id) VALUES 
	(1, 1),
    (2, 2),
    (3, 3),
    (4, 1);

INSERT INTO ArtistAlbums (artist_id, album_id) VALUES 
	(1, 1),
    (2, 2),
    (3, 3), 
    (4, 1); 

INSERT INTO CollectionTracks (collection_id, track_id) VALUES 
	(1, 1),
    (1, 2), 
    (2, 3), 
    (2, 4), 
    (3, 5), 
    (3, 6); 

SELECT name, song_duration 
FROM Tracks 
ORDER BY song_duration DESC 
LIMIT 1;

SELECT name 
FROM Tracks 
WHERE song_duration >= '00:03:30';

SELECT name 
FROM Tracks 
WHERE song_duration >= '00:03:30';

SELECT name 
FROM Collections 
WHERE release_year BETWEEN '2018-01-01' AND '2020-12-31';

SELECT name 
FROM Artists 
WHERE name NOT LIKE '% %';

SELECT name 
FROM Tracks 
WHERE name ILIKE '%мой%' OR name ILIKE '%my%';

SELECT g.name AS genre, COUNT(ag.artist_id) AS number_of_artists
FROM Genres g
JOIN ArtistGenres ag ON g.id = ag.genre_id
GROUP BY g.name;

SELECT g.name AS genre, COUNT(ag.artist_id) AS number_of_artists
FROM Genres g
JOIN ArtistGenres ag ON g.id = ag.genre_id
GROUP BY g.name;

SELECT a.name AS album, AVG(t.song_duration) AS average_duration
FROM Albums a
JOIN Tracks t ON a.id = t.album_id
GROUP BY a.name;

SELECT a.name AS album, AVG(t.song_duration) AS average_duration
FROM Albums a
JOIN Tracks t ON a.id = t.album_id
GROUP BY a.name;

SELECT ar.name AS artist
FROM Artists ar
WHERE ar.id NOT IN (
    SELECT aa.artist_id
    FROM ArtistAlbums aa
    JOIN Albums a ON aa.album_id = a.id
    WHERE a.release_year BETWEEN '2020-01-01' AND '2020-12-31'
);

SELECT c.name AS collection
FROM Collections c
JOIN CollectionTracks ct ON c.id = ct.collection_id
JOIN Tracks t ON ct.track_id = t.id
JOIN Albums a ON t.album_id = a.id
JOIN ArtistAlbums aa ON a.id = aa.album_id
JOIN Artists ar ON aa.artist_id = ar.id
WHERE ar.name = 'Eminem';

SELECT a.name AS album
FROM Albums a
JOIN ArtistAlbums aa ON a.id = aa.album_id
JOIN Artists ar ON aa.artist_id = ar.id
WHERE (
    SELECT COUNT(*)
    FROM ArtistGenres ag
    WHERE ag.artist_id = ar.id
) > 1;

SELECT a.name AS album
FROM Albums a
JOIN ArtistAlbums aa ON a.id = aa.album_id
JOIN Artists ar ON aa.artist_id = ar.id
WHERE (
    SELECT COUNT(*)
    FROM ArtistGenres ag
    WHERE ag.artist_id = ar.id
) > 1;

SELECT ar.name AS artist
FROM Artists ar
JOIN ArtistAlbums aa ON ar.id = aa.artist_id
JOIN Albums a ON aa.album_id = a.id
JOIN Tracks t ON a.id = t.album_id
WHERE t.song_duration = (
    SELECT MIN(t2.song_duration)
    FROM Tracks t2
);

SELECT ar.name AS artist
FROM Artists ar
JOIN ArtistAlbums aa ON ar.id = aa.artist_id
JOIN Albums a ON aa.album_id = a.id
JOIN Tracks t ON a.id = t.album_id
WHERE t.song_duration = (
    SELECT MIN(t2.song_duration)
    FROM Tracks t2
);


