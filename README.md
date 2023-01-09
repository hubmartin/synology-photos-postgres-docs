# Trying to document Synology Photos Posgres database structure

Connect cheatsheet
https://gist.github.com/josefglatz/dd6ad5e6c146b6d389ab64df3b8c720d

Postgres cheatsheet 
https://www.postgresqltutorial.com/postgresql-cheat-sheet/

Example script how to create album, add photos, set coverphoto and also backup database
https://community.synology.com/enu/forum/1/post/148949

# Allow external connection

https://community.synology.com/enu/forum/17/post/76349

I use DBeaver as a GUI to manage postgre, because pgAdmin was taking 30 seconds for every request, even listing tables.., weird.

```
1) Install the latest pgAdmin (PostgreSQL GUI)
2) Go to Synology Security settings->Firewall and open PostgreSQL default port: 5432 (Source) TCP
3) Go to /etc/postgresql in your SSH terminal and modify these two files (always make a backup):
pga_hba.conf:
Add this line:
host all all <YourComputerIP>/32 trust

In postgresql.conf set the listen_address to:
listen_addresses = '*'
4) Restart postgres server: From your SSH client run these commands
>su posgtgres
>pg_ctl -m fast restart

After restarting you can check your log file to see if there are any errors:
tail -f /var/log/postgresql.log

5) From your computer test if the PostgreSQL port is open: telnet <Synology IP> 5432
6) Run pgAdmin and open a server connection with these parameters
Host: your synology IP
Maintenance DB: mediaserver
Username: postgres

And you will have access to the indexing DB! Be careful not to modify anything that may interfere with the indexing mechanism.
```



Database is synofoto. This is not a typo, another databse synophoto is for older SynologyMoments.

Connect to database by typing `\c synofoto`.

List tables by typing `\dt`

Tables:
```
                           List of relations
 Schema |                 Name                 | Type  |     Owner
--------+--------------------------------------+-------+----------------
 public | acl_permission                       | table | SynologyPhotos
 public | activity                             | table | SynologyPhotos
 public | address                              | table | SynologyPhotos
 public | administrative                       | table | SynologyPhotos
 public | album                                | table | SynologyPhotos
 public | aperture                             | table | SynologyPhotos
 public | background_task                      | table | SynologyPhotos
 public | burst_additional                     | table | SynologyPhotos
 public | camera                               | table | SynologyPhotos
 public | check_album_task                     | table | SynologyPhotos
 public | cluster                              | table | SynologyPhotos
 public | condition_album                      | table | SynologyPhotos
 public | config                               | table | SynologyPhotos
 public | delete_album                         | table | SynologyPhotos
 public | delete_condition_album               | table | SynologyPhotos
 public | delete_item                          | table | SynologyPhotos
 public | exposure_time                        | table | SynologyPhotos
 public | face                                 | table | SynologyPhotos
 public | file_operation_error                 | table | SynologyPhotos
 public | file_operation_task                  | table | SynologyPhotos
 public | filter                               | table | SynologyPhotos
 public | focal_length                         | table | SynologyPhotos
 public | folder                               | table | SynologyPhotos
 public | folder_operation_error               | table | SynologyPhotos
 public | folder_operation_task                | table | SynologyPhotos
 public | general_tag                          | table | SynologyPhotos
 public | geocoding                            | table | SynologyPhotos
 public | geocoding_info                       | table | SynologyPhotos
 public | group_info                           | table | SynologyPhotos
 public | index_queue                          | table | SynologyPhotos
 public | iso                                  | table | SynologyPhotos
 public | item                                 | table | SynologyPhotos
 public | lens                                 | table | SynologyPhotos
 public | live_additional                      | table | SynologyPhotos
 public | many_item_has_many_normal_album      | table | SynologyPhotos
 public | many_unit_has_many_administrative    | table | SynologyPhotos
 public | many_unit_has_many_general_tag       | table | SynologyPhotos
 public | many_unit_has_many_person            | table | SynologyPhotos
 public | metadata                             | table | SynologyPhotos
 public | mobile_config                        | table | SynologyPhotos
 public | normal_album                         | table | SynologyPhotos
 public | notification                         | table | SynologyPhotos
 public | one_filter_has_many_unit             | table | SynologyPhotos
 public | person                               | table | SynologyPhotos
 public | person_group                         | table | SynologyPhotos
 public | person_item_count                    | table | SynologyPhotos
 public | person_migration_mapping             | table | SynologyPhotos
 public | photo_request                        | table | SynologyPhotos
 public | search_timeline                      | table | SynologyPhotos
 public | share                                | table | SynologyPhotos
 public | share_permission                     | table | SynologyPhotos
 public | takentime                            | table | SynologyPhotos
 public | team_library_folder_has_many_sorting | table | SynologyPhotos
 public | team_library_group_permission        | table | SynologyPhotos
 public | team_library_user_permission         | table | SynologyPhotos
 public | thumb_preview                        | table | SynologyPhotos
 public | thumbnail                            | table | SynologyPhotos
 public | unit                                 | table | SynologyPhotos
 public | user_flag                            | table | SynologyPhotos
 public | user_info                            | table | SynologyPhotos
 public | version_time                         | table | SynologyPhotos
 public | video_additional                     | table | SynologyPhotos
 public | video_convert                        | table | SynologyPhotos
(63 rows)
```

## unit 
contains ID and filename
columns:

 id   | id_user | type | item_type | filename |  filesize  |  createtime   |   mtime    | takentime  | duplicate_hash | cache_key  | resolution
      | index_stage | version | id_geocoding | id_item | mobile_cache_mtime | reindex_flag | normalized_filename | is_major | id_folder | name_for_sort
      
Most important are id, filename, id_folder

## album

Contain albums

 id | id_user |    name     | type | shared | create_time | cover  | sort_by | sort_direction | normalized_name | v
ersion | passphrase_share | item_count | start_time |  end_time

## many_item_has_many_normal_album

Contains relation betwen `unit` and `album`.


