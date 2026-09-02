# Architecture

## Overview

The File Upload Portal uses a simple Power Platform architecture:

**Human Submitter → Power Apps ↔ Dataverse → Power Automate → Office 365 Outlook**

Microsoft Dataverse is the system of record.

## Components

### Power Apps

**Component:** File Upload Portal

Responsibilities:

- Document and metadata intake
- Required-field validation
- File attachment capture
- Submission history viewing

The Canvas App reads from and writes to Dataverse.

### Microsoft Dataverse

**Operational table:** DocumentUpload  
**Supporting system table:** Users

Responsibilities:

- Stores submission metadata and file content
- Acts as the system of record
- Enforces required business rules
- Provides creator details used by the notification flow

The `Users` system table is used to retrieve the creator's email address. The solution does not directly query Microsoft Entra ID.

### Power Automate

**Component:** Document Upload Notification

Responsibilities:

- Triggers when a new DocumentUpload record is created
- Retrieves the creator's email from Dataverse Users
- Builds the confirmation message
- Sends the confirmation email

### Office 365 Outlook

Office 365 Outlook is the external email service used by the flow to deliver the submission confirmation.

## Data Flow

1. A submitter enters metadata and attaches a document in Power Apps.
2. Power Apps writes the submission to Dataverse.
3. Power Apps also reads Dataverse records for submission history.
4. Creation of a new DocumentUpload record triggers the Document Upload Notification flow.
5. The flow retrieves the creator's Dataverse Users record.
6. The flow sends a confirmation email through Office 365 Outlook.

## Design Decisions

- **Dataverse as system of record:** Submission metadata and file content are stored together in the Power Platform data layer.
- **Dataverse-triggered automation:** Notification processing starts only after the submission record is created.
- **Dataverse Users for recipient lookup:** The flow uses the record creator and the Dataverse Users table rather than introducing a separate identity lookup integration.
- **Minimal integration architecture:** No SharePoint, Azure Storage, AI service, or separate approval layer is required by the implemented solution.
