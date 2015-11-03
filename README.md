App Engine application for the Udacity training course.

## Products
- [App Engine][1]

## Language
- [Python][2]

## APIs
- [Google Cloud Endpoints][3]

## Setup Instructions
1. Update the value of `application` in `app.yaml` to the app ID you
   have registered in the App Engine admin console and would like to use to host
   your instance of this sample.
1. Update the values at the top of `settings.py` to
   reflect the respective client IDs you have registered in the
   [Developer Console][4].
1. Update the value of CLIENT_ID in `static/js/app.js` to the Web client ID
1. (Optional) Mark the configuration files as unchanged as follows:
   `$ git update-index --assume-unchanged app.yaml settings.py static/js/app.js`
1. Run the app with the devserver using `dev_appserver.py DIR`, and ensure it's running by visiting your local server's address (by default [localhost:8080][5].)
1. (Optional) Generate your client library(ies) with [the endpoints tool][6].
1. Deploy your application.


[1]: https://developers.google.com/appengine
[2]: http://python.org
[3]: https://developers.google.com/appengine/docs/python/endpoints/
[4]: https://console.developers.google.com/
[5]: https://localhost:8080/
[6]: https://developers.google.com/appengine/docs/python/endpoints/endpoints_tool

ProjectID is conferencecentral-1080 so link would be http://conferencecentral-1080.appspot.com

Explain Design Choices
1) I made session and speaker both Strings. The speaker is a string because I don't have a list of specific users 
and the speakers could change at any moment. Session was made as a child of the Conference because it will never be orphaned. It will
always need to be tied to a Conference. Furthermore, it will be easier to query, in terms of ancestor.
Please look below for inline explanation of my models.


class Profile(ndb.Model):
    """Profile -- User profile object"""
    #displayName = ndb.StringProperty()
    #mainEmail = ndb.StringProperty()
    #teeShirtSize = ndb.StringProperty(default='NOT_SPECIFIED')
    #conferenceKeysToAttend = ndb.StringProperty(repeated=True)
   * sessionWishlist = ndb.StringProperty(repeated=True) --> Made a string because it will be url safe and is used for searching.
    
class Session(ndb.Model):
   * """Session --Session object"""
   * websafeConferenceKey = ndb.StringProperty() --> made a string because it will be used for searching
   * sessionName = ndb.StringProperty() --> made a string because it can be text or alphanumeric
   * highlights = ndb.StringProperty() --> made a string because it can be text or alphanumeric 
   * speaker = ndb.StringProperty() --> made a string because it can be text or alphanumeric
   * duration = ndb.StringProperty() --> made a string because it will be easier to query off of (initially I was using 1hr or 60m, which is why I didn't use integer)
   * typeOfSession = ndb.StringProperty() --> made a string because input would be text
   * date = ndb.DateProperty() --> made a date to for formatting purposes, yyyy-mm-dd
   * startTime = ndb.TimeProperty() --> made as time because it has to be inputted as 24 hr notation in a specific format


2) Come up with 2 additional queries
-One query looks for Sessions that are about vampires
-The other query looks for sessions that are 60 mins.

3) Issue with Before 7pm and no workshop query. The issue with this query is that you cannot perform more than one inequality filter in a query
 ie !=. You therefore have to break the query up. To resolve this issue, I queried for all sessions that were not a workshop,
 then I looped through the results and compared the time to ensure the time was before 7pm.
