## Affinidi Credential Issuance Service

This document illustrate step by step guide to add Affinidi Iota framework with `nextjs` Framework with `nextauthjs` Library

#### Step 1

Install Dependencies for Affinidi Credentials Issuance Client And Affinidi TDK Auth provider

```javascript
npm install @affinidi-tdk/auth-provider @affinidi-tdk/iota-browser @affinidi-tdk/iota-core
```

- ## Create required directories and files

  ```bash

  mkdir -p src/components/iota
  mkdir -p src/pages/api/iota

  touch src/pages/iota.tsx
  touch src/components/iota/IotaClientPage.tsx
  touch src/pages/api/iota/start.ts


  ```

#### Create Personal Access Token (PAT)

Personal Access Token as its needed for Affinidi TDK Auth provider. You can refer the [Affinidi Documentation](https://docs.affinidi.com/dev-tools/affinidi-cli/manage-token/#affinidi-token-create-token) for more details.

Ignore if allready done configuration for credentials issuance

#### Affinidi Iota Configuration

create Affinidi Iota framework configuration

You can easily do this using the [Affinidi Portal](https://portal.affinidi.com)

1. Login on [Affinidi Portal](https://portal.affinidi.com)

2. Click on Create Configuration and set the following fields:

   `Wallet`: Create a new wallet and provide the new wallet name, or select an existing Wallet that will sign and issue the credentials to the user.

   `Vault JWT Expiration time`: Credential Offers have a limited lifetime to enhance security. Consumers must claim the offer within this timeframe.

3. Optionally, you can configure whether to enable:

   `Enable Verification`: To verify the credentials the user shares using the Credential Verification service.

   `Enable Consent Audit Log`: To store the consent given by the user whenever they share data with the website.

4. After setting the fields and providing the list of the supported schema, click Create.
5. Provide the name of the Presentation Definition and then select from the available templates to pre-populate the editor. You can modify the presentation definition template based on the required data you would like to request from the Affinidi Vault.

for eg

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

6. Click on Save. ConfigurationId & QueryId is generated, update in `.env` file.

#### Step 2

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

#### Step 3

Create a component as Iota Client Page `src/components/iota/IotaClientPage.tsx`

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

#### Step 4

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

#### Step 5

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

#### Step 6

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

#### Step 7

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

#### Step 8

Add the addNewDataRequest function

```javascript
const addNewDataRequest = (request: IotaRequest) => {
  setDataRequest((prevRequests) => ({
    ...prevRequests,
    request,
  }));
};
```

#### Step 9

Add the updateDataRequestWithResponse function

```javascript
const updateDataRequestWithResponse = (response: IotaResponse) => {
  setDataRequest((prevRequests) => ({
    ...prevRequests,
    response,
  }));
};
```

#### Step 10

Update Next Auth frontend Variables `src/lib/variables.ts`

```javascript

export const configurationId = process.env.NEXT_PUBLIC_IOTA_CONFIGURATION_ID!;
export const queryId = process.env.NEXT_PUBLIC_IOTA_QUERY_ID!;
```

#### Step 11

update `.env` file

```javascript
NEXT_PUBLIC_IOTA_QUERY_ID = "";
NEXT_PUBLIC_IOTA_CONFIGURATION_ID = "";
```

### Step 12

Try the App with Affinidi Login & Affinidi CIS

```sh
npm run dev
```

Open [http://localhost:3000/iota](http://localhost:3000/iota) with your browser to see the result.
