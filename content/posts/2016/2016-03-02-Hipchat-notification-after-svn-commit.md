---
date: 2016-03-02
title: "Hipchat notification after SVN commit"
---

Get a channel notification token from the Hipchat control panel.

Put this script under the hooks directory of the SVN repository

	REPOS="$1"
	REV="$2"
	WHO="$(svnlook author -r $REV $REPOS)"
	LOG="$(svnlook log -r $REV $REPOS)"
	
	MESSAGE="Commit to $REPOS by $WHO ($LOG) - https://svn.example.org/blah/$REV/diff"

	ROOM_ID="123456"
	AUTH_TOKEN="token"
	
	curl --silent --insecure -H "Content-Type: application/json" \
	     -X POST \
	     -d "{\"color\": \"green\", \"message_format\": \"text\", \"message\": \"$MESSAGE\" }" \
	     https://api.hipchat.com/v2/room/$ROOM_ID/notification?auth_token=$AUTH_TOKEN

Any commit will trigger the script and you'll receive a message looking like:

	Commit to /home/svn/salt by SEB (this is a commit message) - https://svn.example.org/blah/145/diff
	
