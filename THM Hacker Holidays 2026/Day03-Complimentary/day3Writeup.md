## Day 3

### Complimentary

In this room we are provided an aws hosted website that when we go to it automatically creates a guest account for you and will theoretically begin saving informationa bout you. I inspected the page and found a java script file feeding logic to the page. I opend the java script to see how it creates user accounts

```
// Byte Lotus Wellness â€” guest dashboard
//
// No login screen on purpose: every visitor gets "free" AWS guest
// credentials from our Cognito Identity Pool so we can save wellness
// preferences without the friction of an account.

const IDENTITY_POOL_ID = "us-east-1:836c0949-292d-485b-b532-52d5ca7bb688";
const AWS_REGION = "us-east-1";
const TABLE_NAME = "complimentary-GuestWellnessProfiles";

AWS.config.region = AWS_REGION;
AWS.config.credentials = new AWS.CognitoIdentityCredentials({
  IdentityPoolId: IDENTITY_POOL_ID,
});

function guestId() {
  let id = localStorage.getItem("byteLotusGuestId");
  if (!id) {
    // First visit: hand out a throwaway guest id, same as checking in.
    id = "guest-" + Math.random().toString(36).slice(2, 10);
    localStorage.setItem("byteLotusGuestId", id);
  }
  return id;
}

function renderDashboard(item) {
  const el = document.getElementById("dashboard");
  if (!item) {
    el.textContent = "Welcome! We don't have wellness data for you yet â€” check back after your first spa visit.";
    return;
  }
  el.textContent = [
    "Name: " + (item.name ? item.name.S : "â€”"),
    "Loyalty notes: " + (item.notes ? item.notes.S : "â€”"),
  ].join("\n");
}

AWS.config.credentials.get(function (err) {
  if (err) {
    console.error("Could not fetch guest credentials:", err);
    return;
  }

  const dynamodb = new AWS.DynamoDB({ region: AWS_REGION });
  dynamodb.getItem(
    {
      TableName: TABLE_NAME,
      Key: { guest_id: { S: guestId() } },
    },
    function (err, data) {
      if (err) {
        console.error("Could not load dashboard:", err);
        return;
      }
      renderDashboard(data.Item);
    }
  );
});

```


Full Disclosure from this point: I had no idea how to navigate AWS Dynamo from here. I never have so I followed the video on the room.

So this code basically uses AWS Cognito Identity pool to generate a guest identity that will allow visitors to the site to have a profile without having to go through account creation. This is a fairly common thing to do but you have to be careful with identity and access management when allowing it to do it this way. To exploit vulnerabilities in this IAM I created an account using the command line. 

![alt text](<Screenshots/Screenshot 2026-08-01 162319.png>)

then I gave that temporary identity credentials for a full identity

![alt text](<Screenshots/Screenshot 2026-08-01 162511.png>)
I had to export those identity details to my machine to be able to access AWS as that user.
When I checked my caller id I realized I had made a typo in pulling the info

![alt text](<Screenshots/Screenshot 2026-08-01 162843.png>)
and now with a user id I was able to scan the associated table with guest wellness profiles for the site. ![alt text](<Screenshots/Screenshot 2026-08-01 163041.png>)

And the flag was found in one of the saved profiles