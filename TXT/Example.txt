# A Comprehensive Example

First, here is the Claim Template for a membership card (CT1):
```json
{
   "title": "Membership Card",
   "type": "object",
   "properties": {
      "variables": {
         "type": "object",
         "properties": {
            "memberName": {
               "type": "string",
               "description": "Name of the member"
            },
            "memberIDS": {
               "type": "string",
               "description": "IDS of the member"
            },
            "membershipID": {
               "type": "string",
               "description": "Unique identifier for the membership"
            },
            "startDate": {
               "type": "string",
               "format": "date",
               "description": "Start date of the membership"
            },
            "endDate": {
               "type": "string",
               "format": "date",
               "description": "End date of the membership"
            },
            "photoAttachment": {
               "type": "string",
               "description": "Personal photo of the member",
               "format": "attachment",
               "additionalProperties": {
                   "hash": {
                       "type": "string",
                       "description": "Hash of the photo file for verification"
                   }
               }
            }
         },
         "required": ["memberName","memberIDS", "membershipID", "startDate", "endDate", "photoAttachment"]
      },
      "Simple Presentation Format": {
         "type": "string",
         "description": "Concise representation of the membership card details",
         "const": "Membership Card for {memberName:string} ({memberIDS:string}) with ID {membershipID:string}. Valid from {startDate:date} to {endDate:date}. Photo: {photoAttachment:attachment}"
      }
   },
   "required": ["variables", "Simple Presentation Format"]
}
```

An example ATT of the CT1 (VAT1):
```json
{
   "ATT": {
      "AT": {
         "CL": {
            "Claim Template": "Membership Card",
            "variableValues": {
               "memberName": "John Doe",
               "memberIDS": "47034078473098"
               "membershipID": "12345XYZ",
               "startDate": "2023-01-01",
               "endDate": "2024-01-01",
               "photoAttachment": {
                  "fileName": "JohnDoePhoto.jpg",
                  "hash": "5d41402abc4b2a76b9719d911017c592"
               }
            }
         },
         "IDS": "Cary Country Club"
      },
      "POA": {
         "Type": "DDS",
         "ID": "DDS9876ABC",
         "IDS": "Cary Country Club"
      }
   }
}
```

The ReaderFriendlyFormat of VAT1 is:
```
ATT - Membership Card:
   Member Name: John Doe
   Member IDS: 47034078473098
   Membership ID: 12345XYZ
   Validity Period: From 2023-01-01 to 2024-01-01
   Photo Attachment: JohnDoePhoto.jpg (hash: 5d41402abc4b2a76b9719d911017c592)
Attested by: 
   Cary Country Club [POA ID: DDS9876ABC]
```

The SimplePresentation of VAT1 is:

`\Membership Card for {memberName:John Doe} ({memberIDS:47034078473098}) with ID {membershipID:12345XYZ}. Valid from {startDate:2023-01-01} to {endDate:2024-01-01}. Photo: {photoAttachment:attachment JohnDoePhoto.jpg:5d41402abc4b2a76b9719d911017c592} \Cary Country Club[DDS9876ABC]/`

The SimplePresentation of a CVFP of VAT1 (VF1) is:

`CVF(\Membership Card for {memberName:$IGNORE} ({memberIDS:$HOLDER}) with ID {membershipID:$IGNORE}. Valid from {startDate:$IGNORE} to {endDate:$IGNORE}. Photo: {photoAttachment:attachment $IGNORE} \Cary Country Club/, $IGNORE)`
