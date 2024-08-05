## Affinidi Iota Framwork

A framework that provides a secured and simplified data-sharing process from Affinidi Vault with user consent for enhanced user experience.The Affinidi Iota Framework leverages the OID4VP (OpenID for Verifiable Presentation) standard to request and receive data from Affinidi Vault. The OID4VP is built with the OAuth 2.0 authorisation framework, providing developers with a simple and secure presentation of credentials.

More Details on Affinidi Iota Framework is available on [Affinidi Documentation](https://docs.affinidi.com/frameworks/iota-framework/)

## Introduction

We will use a simple [Next.js](https://nextjs.org/) app and enable Affinidi Iota framework in `iota` page with `nextjs` Framework.

## Pre-Requisite

To complete this workshop, you would require a devleoper account at Affinidi Portal and few tools as lsited below.

- An account on [Affinidi portal](https://portal.affinidi.com/)
- [Affinidi cli](https://docs.affinidi.com/dev-tools/affinidi-cli/)
- Implement [Affinidi Login](/affinidi-login.md) to the sample Application created from this workshop
- Implement [Affinidi Credentials Issuance](/credentials-issuance.MD) to the sample application created from this workshop

You need to have the following installed on your machine:

- [NodeJs v18 and higher](https://nodejs.org). (it's recommended to use [nvm](https://github.com/nvm-sh/nvm))
- [VS Code](https://code.visualstudio.com/) or any familar IDE for development.

More details here in [Affinidi Documentation](https://docs.affinidi.com/frameworks/iota-framework/)

<div style="border: 2px solid green; padding: 10px; border-radius: 5px; background-color: #cefad0;">
  <strong>Note</strong>
  <p>This sample App is extension of same App that we worked on for Affinidi Login and Affinidi Credentials Issuance Service.
  </p>
</div>
&nbsp;

## Step by Step Guide to enable Affinidi Iota framework

Before proceeding with the steps below, make sure you have completed the pre-requisites mentioned above.

<div style="border: 2px solid red; padding: 10px; border-radius: 5px; background-color: #ffe6e6;">
  <strong>Important Note</strong>
  <p>The steps showcased to this sample application is provided only as a guide to quickly explore and learn how to integrate the components of Affinidi Trust Network into your application. This is NOT a Production-ready implementation. Do not deploy this to a production environment.
  </p>
</div>
&nbsp;

Now, let's continue with the step-by-step guide to enable Affinidi Iota framework in sample App.

### Install Dependencies

Install Dependencies for `Affinidi Iota framework` which is `iota-browser` & `iota-core`

```sh
npm install @affinidi-tdk/iota-browser @affinidi-tdk/iota-core
```

<div style="border: 2px solid green; padding: 10px; border-radius: 5px; background-color: #cefad0;">
  <strong>Note</strong>
  <p>It also require Affinidi TDK Auth Provider, however we already have installed that package as part of Affinidi Credential Issuance service.
  </p>
</div>
&nbsp;

### Create required directories and files

```sh

mkdir -p src/components/iota
mkdir -p src/pages/api/iota

touch src/pages/iota.tsx
touch src/components/iota/IotaClientPage.tsx
touch src/pages/api/iota/start.ts


```

### Create Personal Access Token (PAT)

Personal Access Token (PAT) is like a machine user that acts on your behalf to the Affinidi services. You can use the PAT to authenticate to the Affinidi services and automate specific tasks within your application. A Personal Access Token (PAT) lives outside of Projects, meaning PAT can access multiple projects once granted by the user.

More Details on [Personal Access Token](https://docs.affinidi.com/dev-tools/affinidi-cli/manage-token/#how-does-pat-authentication-works) here.

Personal Access Token is its needed for `Affinidi TDK Auth provider`.

You can refer the [Affinidi Documentation](https://docs.affinidi.com/dev-tools/affinidi-cli/manage-token/#affinidi-token-create-token) for creating pesronal access token from CLI.

<div style="border: 2px solid green; padding: 10px; border-radius: 5px; background-color: #cefad0;">
  <strong>Note</strong>
  <p>we already have create personal access token (PAT) as part of Affinidi Credential Issuance service. we will reuse the same token for Affinidi Iota framework as well.
  </p>
</div>
&nbsp;

### Configure Affinidi Iota Configuration

create Affinidi Iota framework configuration

You can easily do this using the [Affinidi Portal](https://portal.affinidi.com)

1. Login on [Affinidi Portal](https://portal.affinidi.com)

2. Click on Create Configuration and set the following fields:

   `Wallet`: Create a new wallet and provide the new wallet name, or select an existing Wallet that will sign and issue the credentials to the user.

   `Vault JWT Expiration time`: Credential Offers have a limited lifetime to enhance security. Consumers must claim the offer within this timeframe.

3. Optionally, you can configure whether to enable:

   `Enable Verification`: To verify the credentials the user shares using the Credential Verification service.

   `Enable Consent Audit Log`: To store the consent given by the user whenever they share data with the website.

4. After setting the fields and providing the list of the supported schema, click `Create`.
5. Provide the name of the Presentation Definition and then select from the available templates to pre-populate the editor. You can modify the presentation definition template based on the required data you would like to request from the Affinidi Vault.

**for eg**

```javascript
{
  "id": "token_with_workshopSchema",
  "input_descriptors": [
    {
      "id": "workshopSchema",
      "name": "Workshop VC",
      "purpose": "Check if Vault contains the required VC.",
      "constraints": {
        "fields": [
          {
            "path": [
              "$.type"
            ],
            "purpose": "Check if VC type is correct",
            "filter": {
              "type": "array",
              "contains": {
                "type": "string",
                "pattern": "TworkshopSchemaV1R0"
              }
            }
          }
        ]
      }
    }
  ]
}
```

6. Click on Save. `ConfigurationId` & `QueryId` is generated.

<div style="border: 2px solid green; padding: 10px; border-radius: 5px; background-color: #cefad0;">
  <strong>Note</strong>
  <p>The above PEX query provide response only in case affinidi vault has claimed credentials using Affinidi Credential Issuance enabled from the previous workshop steps.
  </p>
</div>
&nbsp;

### Update environment variables

update `.env` file with frontend variables

```
NEXT_PUBLIC_IOTA_QUERY_ID = ""
NEXT_PUBLIC_IOTA_CONFIGURATION_ID = ""
```

## Implement Application Code Changes

### Update frontend env reader

Update Next Auth frontend Variables `src/lib/variables.ts`

```javascript
export const configurationId = process.env.NEXT_PUBLIC_IOTA_CONFIGURATION_ID!;
export const queryId = process.env.NEXT_PUBLIC_IOTA_QUERY_ID!;
```

### Create `/iota` page with `service-side` rendering as `false`

Create new routes `src/pages/iota.tsx` and put dynamic content from Next Auth and `SSR : False`

```javascript
import dynamic from "next/dynamic";

const IotaClientPage = dynamic(
  () => import("../components/iota/IotaClientPage"),
  {
    ssr: false,
  }
);
export default function Page() {
  return <IotaClientPage />;
}
```

### Add `components` for rendering the page `IotaClientPage` with `Button` to Initiate Iota framework

Create a component as Iota Client Page `src/components/iota/IotaClientPage.tsx`

```javascript
import { useState } from "react";
import {
  IotaCredentials,
  IotaRequest,
  IotaResponse,
  OpenMode,
  Session,
} from "@affinidi-tdk/iota-browser";
import { configurationId, queryId } from "@/lib/variables";

type DataRequest = {
  request?: IotaRequest,
  response?: IotaResponse,
};
```

```javascript
export default function IotaTesting() {
  const containerStyle: React.CSSProperties = {
    backgroundColor: "auto",
    height: "100vh",
  };
  return (
    <div style={containerStyle}>
      <h1 className="text-2xl font-semibold pb-6">Affinidi Iota Framework</h1>
      {/* Step 1: Add a button to trigger the handleIotaShare function */}
      <button
        className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 mr-5"
        onClick={handleIotaShare} // Step 2: Add the handleIotaShare function to the button
      >
        Click to Share
      </button>

      <button
        type="submit"
        onClick={() => window.open("/", "_self")}
        className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
      >
        Return to Homepage
      </button>

      {dataRequest?.response && (
        <>
          <br />
          <br />
          <h3 className="text-2xl font-semibold pb-6">Response</h3>{" "}
          {/* Display the response */}
          <pre>{JSON.stringify(dataRequest.response, null, 2)}</pre>
        </>
      )}
    </div>
  );
}
```

### Apply Action on Button Click `handleIotaShare` function

Create `handleIotaShare` function which invoke Iota framework

```javascript
  const [iotaIsInitializing, setIotaIsInitializing] = useState(false);
  const [iotaSession, setIotaSession] = useState<Session | undefined>(
    undefined
  );
  const [dataRequest, setDataRequest] = useState<DataRequest>(); // Data requests and responses


  const handleIotaShare = async () => {
    try {
      setIotaIsInitializing(true);
      const session = await createIotaSession();
      if (!session) {
        throw new Error("IotaSession not initialized");
      }
      const request = await session.prepareRequest({ queryId });
      console.log("Request", request);
      addNewDataRequest(request);
      request.openVault({ mode: OpenMode.Popup }); // Open the vault in a popup, Option are 1)NewTab, 2)Popup
      setIotaIsInitializing(false);
      const response = await request.getResponse();
      console.log("Response", response);
      if (response) {
        updateDataRequestWithResponse(response);
      }
    } catch (e) {
      console.error("Error", e);
      setIotaIsInitializing(false);
    }
  };
```

### Create `IotaSession`

Add the createIotaSession function

```javascript
const createIotaSession = async () => {
  try {
    if (iotaSession) {
      console.log("Iota session already exists");
      return iotaSession;
    }
    console.log("======Iota Session Creating=========");
    console.log("configurationId", configurationId);
    const credentialResponse = await getIotaCredentials(configurationId);
    console.log("credentialResponse", credentialResponse);
    const session = new Session({ credentials: credentialResponse });
    console.log("session", session);
    await session.initialize();
    setIotaSession(session);
    return session;
  } catch (error) {
    setIotaSession(undefined);
    console.error("Error initializing Iota Session:", error);
  }
};
```

### Create frontend API to pass details for `Iota Limited Token`

Create function getIotaCredentials function

```javascript

  async function getIotaCredentials(configurationId: string) {
    const response = await fetch(
      "/api/iota/start?" +
        new URLSearchParams({
          iotaConfigurationId: configurationId,
        }),
      {
        method: "GET",
      }
    );
    return (await response.json()) as IotaCredentials;
  }

```

#### Create backend API to call `Affinidi TDK` for Affinidi Auth Provider and Affinidi Iota Core for creating `Iota limited token`

Create the API to start the Iota Limited Token `src/pages/api/iota/start.ts`

```javascript
import { IotaCredentials, Iota } from "@affinidi-tdk/iota-core";
import type { NextApiRequest, NextApiResponse } from "next";
import { AuthProvider } from "@affinidi-tdk/auth-provider";
import { passphrase, privateKey, projectId, tokenId } from "@/lib/env";
import { getServerSession } from "next-auth";
import { authOptions } from "@/lib/auth/auth-options";
interface ResponseError {
  message: string;
}

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse<IotaCredentials | ResponseError>
) {
  try {
    const session = await getServerSession(req, res, authOptions);
    if (!session) {
      res.status(401).json({ message: "You must be logged in." });
      return;
    }
    const authProvider = new AuthProvider({
      privateKey,
      passphrase,
      tokenId,
      projectId,
    });

    const { iotaConfigurationId } = req.query;
    const iotaToken = authProvider.createIotaToken(
      iotaConfigurationId as string,
      session.userId
    );
    const iotaCredentials = await Iota.limitedTokenToIotaCredentials(
      iotaToken.iotaJwt
    );

    res.status(200).json(iotaCredentials);
  } catch (error: any) {
    res.status(500).json({ message: "Unable to get Iota credentials" });
    console.log(error);
  }
}

```

### Add New data request

Add the addNewDataRequest function

```javascript
const addNewDataRequest = (request: IotaRequest) => {
  setDataRequest((prevRequests) => ({
    ...prevRequests,
    request,
  }));
};
```

### Update the data Request with Response

Add the updateDataRequestWithResponse function

```javascript
const updateDataRequestWithResponse = (response: IotaResponse) => {
  setDataRequest((prevRequests) => ({
    ...prevRequests,
    response,
  }));
};
```

### Run The application to experience Affinidi Iota framework

Try the App with Affinidi Login & Affinidi Iota Framework

```sh
npm run dev
```

Open [http://localhost:3000/iota](http://localhost:3000/iota) with your browser to see the result.
