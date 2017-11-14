# cxa_exif

Directories of scans are in the form:

```
YYYYMMDD-YYYYMMDD (Location A,Location B) RollNumber
```

Scans files are in the form:

```
YYYY-MM-DD_Camera_Film-Speed+Push_Roll_Frame_Lens.tif
```

For example:

```
2017-03-23_M6_HP5-400+2_8187_03_Nokton35.tif
```

The `Data.yml` file must be up to date with the *Camera*, *Film*, and *Lens*
used in the file names.

Install the dependencies with:

```sh
bundle install
```

Run the EXIF task with:


```sh
bundle exec rake exif["/path/to/images"]
```
