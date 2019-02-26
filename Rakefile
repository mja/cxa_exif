# coding: utf-8
require 'rubygems'
require 'yaml'
require 'multi_exiftool'

data = YAML.load_file("Data.yml")

cameras = data[:cameras]
lenses = data[:lenses]
films = data[:films]

creator = YAML.load_file("Creator.yml")[:creator]

desc "Add EXIF data"
task :exif, [:dir] do |t, args|
  exit_code = 0
  scan_dir = args[:dir]

  if File.directory?(scan_dir)
    tiffs = Dir.glob("#{scan_dir}/**/*.tif")
    exifs, errors = MultiExiftool.read(tiffs)

    exifs.each do |exif|
      sourcefile = exif["sourcefile"]
      filename = exif["filename"]
      directory = exif["directory"]
      roll_name = directory.split(File::SEPARATOR)[-1]
      locations = /[0-9\ ]+ \((?<locs>.*)\)/.match(roll_name)[:locs].split(",")

      description = locations.join(", ")

      settings = /(?<date>[0-9-]+)_(?<camera>[A-Za-z0-9]+)_(?<film>[A-Za-z0-9]+)-(?<speed>[0-9]+)(?<push_pull>[\+|-][0-9]+)?_(?<roll>[0-9]+)_(?<frame>[0-9]+)_(?<lens>[A-Za-z0-9]+)/.match(filename)

      dateoriginal = settings[:date]
      camera = cameras[settings[:camera]]

      box_speed = settings[:speed].to_i
      push_pull = /(?<dir>[-\+])(?<stops>[0-9]+)/.match(settings[:push_pull])

      if push_pull.nil?
        iso = box_speed
      else
        dir = push_pull[:dir] === "+" ? 1 : -1
        stops = push_pull[:stops].to_i
        iso = box_speed * 2 ** (dir * stops)
      end

      film = films[settings[:film]]
      film_name = "#{film[:name]} #{box_speed}"

      lens = lenses[settings[:lens]]

      keywords = locations + [lens[:model], lens[:name], film_name,
                              "#{lens[:focal_length]}mm"]

      values = {
        make: camera[:make],
        model: camera[:model],
        iso: iso,
        lensmodel: lens[:name],
        focallength: lens[:focal_length],
        datetimeoriginal: "#{dateoriginal} 12:00:00 +0000",
        creator: creator,
        title: description,
        description: description,
        keywords: keywords
      }

      errors = MultiExiftool.write(sourcefile, values, overwrite_original: true)

      if errors.empty?
        puts filename
      else
        puts errors
      end
    end
  else
    puts scan_dir + " does not exist!"
    exit_code = 1
  end

  exit exit_code
end
