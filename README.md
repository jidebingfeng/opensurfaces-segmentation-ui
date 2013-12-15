# OpenSurfaces Segmentation UI
This repository contains the segmentation tool, extracted from the
(OpenSurfaces)[http://opensurfaces.cs.cornell.edu] project as a lightweight
independent tool.  A dummy server backend is included to run the demo.

![](https://github.com/seanbell/opensurfaces-segmentation-ui/blob/master/screenshot.png?raw=true)

## Quickstart (Ubuntu Linux)
1. Install dependencies (coffee-script, django, django compressor, ua parser):
<pre>
sudo ./setup-demo.sh
</pre>

2. Start the local webserver:
<pre>
./run-demo.sh
</pre>

3. Open a web browser and visit `localhost:8000`

The demo should also work on Mac and Windows.  You will have to look at
`setup-demo.sh` and run the equivalent commands for your system.

## Project Notes

The demo project is written for Django 1.4, though it probably will work with
other versions since it uses almost no django APIs.

#### Running the project without Django

The html for the segmentation tool is pieced together from a series of templates in
`example_project/segmentation/templates` by the Django template processor.  I
kept them separate for modularity, but you can always convert them to a static
version by viewing the demo (at `localhost:8000`) and then saving the html
source as a static html file.

The javascript for the tool is compiled from coffeescript files by
`django-compressor` and accessed by the user at a url of the form
`/static/cache/js/*.js`.  You can instead compile the coffeescript files into a
single javascript file to get rid of this dependency.  To do this, run these
commands:
<pre>
	cd example_project/segmentation/static/js
	coffee --bare --join build.js --compile $(find . -name '*.coffee')
</pre>
It will generate `bare.js` which you can include in your static html file.
In your static html file, make sure to remove the old `/static/cache/js/*.js`
files.

Otherwise, there is no inherent dependency on Django.

#### If you are building on top of this repository:
In `example_project/settings.py`, change `SECRET_KEY` to some
random string.

#### If you want to add this demo to your own (separate) Django project:
In `example_project/settings.py`, make the following changes:

1. Make sure `STATIC_ROOT` is set to an absolute writeable path.

2. Add this to the `STATICFILES_FINDERS` tuple:
<pre>
	'compressor.finders.CompressorFinder',
</pre>

3. Add this to the `INSTALLED_APPS` tuple:
<pre>
	'django.contrib.humanize',
	'compressor',
	'segmentation'
</pre>

4. Add this to `settings.py` (e.g. at the end):
<pre>
	# Django Compressor
	COMPRESS_ENABLED = True
	COMPRESS_OUTPUT_DIR = 'cache'
	COMPRESS_PRECOMPILERS = (
		('text/coffeescript', 'coffee --bare --compile --stdio'),
		('text/less', 'lessc -x {infile} {outfile}'),
	)
</pre>
