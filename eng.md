Say you have a **time** you'd like to display on your site. You've entered it in
*your* time zone. You can always be specific and say something like: **3:00 PM
Eastern Standard Time**. Then leave it up to whoever is reading to convert it
to their local time.[Every Time Zone][1] is a great site for that.

But it can be nice to localize the time *for* the reader. Time zone conversion
is notoriously confusing. And it's the type of thing computers are good at. 
JavaScript know what time zone the reader is in, after all.

I'm sure there are a number of ways to do this. I had to do it recently on a
new project, and I followed Dave Rupert's lead, who implemented it on
[ShopTalk's website][2] to display the localized time for our next live show.

### Libraries

[Moment.js][3] and [Moment Timezone][4] are a pair that can get this done. And
if you need to do any other heavier date and time manipulation or relative 
calculations (e.g. "32 minutes ago", "12 de Agosto de 2015"), you'll be in good 
hands with these libraries.

We'll also need to detect the time zone itself, and thankfully there is 
[a library for that][5].

So we'll be using:

*   jstz.js
*   moment.js
*   moment-timezone.js

Package or link those up however you will.

### Step 1: Get the Timezone

Might as well stash it in localStorage after you've gotten it. That requires
less effort on the browser's part to retrieve next time.

    if (!sessionStorage.getItem('timezone')) {
      var tz = jstz.determine() || 'UTC';
      sessionStorage.setItem('timezone', tz.name());
    }
    var currTz = sessionStorage.getItem('timezone');

### Step 2: Get a Time Value

Moment expects a date/time value (in [UTC][6]) like:

    2015-08-12T14:30Z

If we're only interested in the time, we can create the date portion of that
string from Moment itself of today's date (just makes the formatting way easier
). The date actually kinda matters since UGHGKGJHGH daylight savings.

    var date = moment().format("YYYY-MM-DD");

Then we'll create the final string, assuming we have a variable with the time
we want:

    var stamp = date + "T" + theTime + "Z";

And turn it into a Moment object:

    var momentTime = moment(stamp);

### Step 3: Localize the time

Now we can adjust/localize that time with Moment Timezone:

    var tzTime = momentTime.tz(currTz);

And properly format it for display:

    var formattedTime = tzTime.format('h:mm A');

### Step 4: Use it

Now we can drop it on the page anywhere we want. If you output the time zone as
well, you could even replace the time currently output on the page, since even 
if detecting the time zone fails it will fall back to UTC and output that as the
time zone.

    output.textContent = "Time in " + currTz + ": " + formattedTime;

### Adjusting Times

Sometimes it makes sense (perhaps from a CMS perspective) to ask people to
input time in a local time zone. But in order to do the conversion correctly, it
makes more sense to have that time in UTC.

Moment itself can help with adjusting times, like:

    moment().subtract(4, 'hours');
    moment().add(3, 'hours');

Server side languages can help too. For instance PHP can do math on times:

    <?php var $sixHoursAgo = strtotime("-6 hours", time()); ?>

It probably makes the most sense to do that with [Moment Timezone][4], because
it's not always an exact formula (again UGHADKGHK daylight savings
). 

### Demo

In this demo we use an HTML5 time input and adjust it on the fly to a localized
time.

See the Pen [Adjusting Times to Local Time Zones][7] by Chris Coyier (
[@chriscoyier][8]) on [CodePen][9].

### Other Ways

Do you have any experience with this? How did you handle it? 

The dependencies we used here are not insignificant! It appears there are
native JavaScript methods (e.g.[getTimezoneOffset()][10]) that have pretty good
support that could be used to similar effect. Any experience relying on those?

[Twitter][11] [Facebook][12] [Google+][13]

 [1]: http://everytimezone.com/
 [2]: http://shoptalkshow.com/
 [3]: http://momentjs.com/
 [4]: http://momentjs.com/timezone/
 [5]: https://bitbucket.org/pellepim/jstimezonedetect
 [6]: https://en.wikipedia.org/wiki/Coordinated_Universal_Time
 [7]: http://codepen.io/chriscoyier/pen/WvWeNa/
 [8]: http://codepen.io/chriscoyier
 [9]: http://codepen.io

 [10]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getTimezoneOffset

 [11]: https://twitter.com/intent/tweet?text=Localizing%20Times%20in%20JavaScript&url=https://css-tricks.com/localizing-times-in-javascript/&via=real_css_tricks

 [12]: https://www.facebook.com/sharer/sharer.php?u=https://css-tricks.com/localizing-times-in-javascript/

 [13]: https://plus.google.com/share?url=https://css-tricks.com/localizing-times-in-javascript/