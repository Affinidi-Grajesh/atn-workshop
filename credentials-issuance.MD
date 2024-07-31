## Affinidi Credential Issuance Service

This document illustrate step by step guide to add Affinidi Credentials Issuance Service with `nextjs` Framework with `nextauthjs` Library

#### Step 1

Install Dependencies for Affinidi Credentials Issuance Client And Affinidi TDK Auth provider

```javascript
npm install @affinidi-tdk/auth-provider @affinidi-tdk/credential-issuance-client
```

- ## Create required directories and files

  ```bash

  mkdir -p src/pages/api/clients/
  mkdir -p src/pages/api/credentials/

  touch src/pages/credentials.tsx
  touch src/pages/api/credentials/issuance-start.ts
  touch src/pages/api/clients/credentials-client.ts
  touch src/lib/variables.ts

  ```

#### Create Personal Access Token (PAT)

Personal Access Token as its needed for Affinidi TDK Auth provider. You can refer the [Affinidi Documentation](https://docs.affinidi.com/dev-tools/affinidi-cli/manage-token/#affinidi-token-create-token) for more details.

for eg :

1. Log in to Affinidi CLI by running

```sh
affinidi start
```

2. Once logged in successfully, create token by running below command

```sh
affinidi token create-token -n MyAppVerificationKey -w -p my-secret-passphrase
```

`MyAppVerificationKey` : Replace with your own Name of the Personal Access Token, at least 8 chars long

`my-secret-passphrase` : Replace with your own Passphrase for generation of private public key pair

Sample response:

```json
{
  "apiGatewayUrl": "https://apse1.api.affinidi.io",
  "tokenEndpoint": "https://apse1.auth.developer.affinidi.io/auth/oauth2/token",
  "keyId": "XXXXXXXXXXXXXXXX",
  "tokenId": "XXXXXXXXXXXXXXXX",
  "passphrase": "XXXXXXXXXXXXXXXX",
  "privateKey": "XXXXXXXXXXXXXXXX",
  "publicKey": "XXXXXXXXXXXXXXXX",
  "projectId": "XXXXXXXXXXXXXXXX"
}
```

For more details on the command run below command

```sh
affinidi token create-token --help
```

#### Configure Credential Issuance Configuration

To issue a Verifiable Credential, it is required to setup the **Issuance Configuration** on your project, where you select the **issuing wallet** and **supported schemas** to create a credential offer that the application issue

You can easily do this using the [Affinidi Portal](https://portal.affinidi.com)

1. Login on [Affinidi Portal](https://portal.affinidi.com)

2. Open `Wallets` menu under the `Tools` section and click on `Create Wallet` with any name (e.g. `MyWallet`) and DID method as `did:key`.

For more information, refer to the [Wallets documentation](https://docs.affinidi.com/dev-tools/wallets)

3. Go to `Credential Issuance Service` under `Services` section.

4. Click on `Create Configuration` and set the following fields:

   `Issuing Wallet`: Select Wallet Created previous step
   `Lifetime of Credential Offer` as `600`

5. Add schemas by clicking on "Add new item" under `Supported Schemas`

Schema 1 :

- _Schema_ as `Manual Input`,
- _Credential Type ID_ as `TworkshopSchemaV1R0`
- _JSON Schema URL_ as `https://schema.affinidi.io/TworkshopSchemaV1R0.json`
- _JSDON-LD Context URL_ = `https://schema.affinidi.io/TworkshopSchemaV1R0.jsonld`

#### Step 2

Create Next Auth frontend Variables `src/lib/variables.ts`

```javascript

export const vaultUrl = process.env.NEXT_PUBLIC_VAULT_URL!;
export const credentialTypeId = process.env.NEXT_PUBLIC_CREDENTIAL_TYPE_ID!;
```

#### Step 3

update env variables `src/lib/env.ts`

```javascript
export const apiGatewayUrl = process.env.API_GATEWAY_URL!;
export const projectId = process.env.PROJECT_ID!;
export const tokenEndpoint = process.env.TOKEN_ENDPOINT!;

export const privateKey = process.env.PRIVATE_KEY!;

export const passphrase = process.env.PASSPHRASE!;
export const tokenId = process.env.TOKEN_ID!;

```

#### Step 4

update `.env` file

```javascript
## frontend only variables

NEXT_PUBLIC_VAULT_URL="https://vault.affinidi.com/claim?credential_offer_uri"
NEXT_PUBLIC_CREDENTIAL_TYPE_ID="TworkshopSchemaV1R0"

## backend only variables

TOKEN_ENDPOINT="https://apse1.auth.developer.affinidi.io/auth/oauth2/token"
API_GATEWAY_URL="https://apse1.api.affinidi.io"

PROJECT_ID=""
TOKEN_ID=""
PASSPHRASE=""
PRIVATE_KEY= ""

```

#### Step 5

Create `src/pages/credentials.tsx` Page

```javascript

import { FC, useEffect, useState } from "react";
import { getSession } from "next-auth/react";
import { Session } from "next-auth";
import { credentialTypeId, vaultUrl } from "@/lib/variables";

type CredentialsProps = {
  email?: string | null | undefined;
  name?: string;
  phoneNumber?: string;
  dob?: string;
  gender?: string;
  address?: string;
  postcode?: string;
  city?: string;
  country?: string;
};

const defaults: CredentialsProps = {
  email: "email",
  name: "name",
  phoneNumber: "phno",
  dob: "dob",
  gender: "gender",
  address: "address",
  postcode: "postcode",
  city: "city",
  country: "country",
};

const Credentials: FC = () => {
  const containerStyle: React.CSSProperties = {
    backgroundColor: "auto",
    height: "100vh",
    display: "flex",
    flexDirection: "column",
    justifyContent: "center",
  };

  const [session, setsession] = useState<Session>();
  const [credentials, setcredentials] = useState<CredentialsProps>(defaults);
  const [issuanceResponse, setissuanceResponse] = useState<any>();
  useEffect(() => {
    async function fetchsession() {
      const mySession = await getSession();
      if (!mySession) {
        console.log("No session found");
        return;
      }
      setsession(mySession);
      console.log("Session found", mySession);
    }
    fetchsession();
  }, []);

//   const handleSubmit = async (event: React.FormEvent) => {
//     event.preventDefault();
//     console.log("Form submitted with credentials: ", credentials);

//     const apiData = {
//       credentialData: {
//         email: credentials.email,
//         name: credentials.name,
//         phoneNumber: credentials.phoneNumber,
//         dob: credentials.dob,
//         gender: credentials.gender,
//         address: credentials.address,
//         postcode: credentials.postcode,
//         city: credentials.city,
//         country: credentials.country,
//       },
//       credentialTypeId: credentialTypeId,
//       holderDid: session?.userId,
//     };

//     const response = await fetch("/api/credentials/issuance-start", {
//       method: "POST",
//       headers: {
//         "Content-Type": "application/json",
//       },
//       body: JSON.stringify(apiData),
//     });

//     if (response.ok) {
//       let DataResponse = await response.json();

//       console.log("Credentials Issued");
//       console.log(DataResponse);
//       setissuanceResponse(DataResponse);
//     } else {
//       console.error("Failed to issue credentials");
//     }
  };

  return (
    <div style={{ ...containerStyle, alignItems: "left" }}>
      <h1 className="text-2xl font-semibold pb-6">
        Affinidi Credentials Issuance
      </h1>
      <p>Holder DID: {session?.userId}</p>
      <br />
      {!issuanceResponse && (
        <form onSubmit={handleSubmit} className="space-y-4">
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Email
            </label>
            <input
              type="text"
              value={session?.user?.email ?? credentials.email ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, email: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
              required
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Name
            </label>
            <input
              type="text"
              value={credentials.name ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, name: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              phoneNumber
            </label>
            <input
              type="text"
              value={credentials.phoneNumber ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, phoneNumber: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Date Of Birth
            </label>
            <input
              type="text"
              value={credentials.dob ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, dob: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Gender
            </label>
            <input
              type="text"
              value={credentials.gender ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, gender: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Postal Code
            </label>
            <input
              type="text"
              value={credentials.postcode ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, postcode: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Address
            </label>
            <input
              type="text"
              value={credentials.address ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, address: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              City
            </label>
            <input
              type="text"
              value={credentials.city ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, city: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div>
            <label className="block text-sm font-medium text-gray-700">
              Country
            </label>
            <input
              type="text"
              value={credentials.country ?? ""}
              onChange={(e) =>
                setcredentials({ ...credentials, country: e.target.value })
              }
              style={{ width: "40%" }}
              className="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <button
            type="submit"
            className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 mr-5"
          >
            Submit
          </button>
          <button
            type="submit"
            onClick={() => window.open("/", "_self")}
            className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          >
            Home
          </button>
        </form>
      )}
      {issuanceResponse && (
        <div>
          <h2>Credentials Issued</h2>
          <br />
          <pre>{JSON.stringify(issuanceResponse, null, 2)}</pre>
          <br />
          <button
            type="submit"
            onClick={() =>
              window.open(
                `${vaultUrl}=${issuanceResponse?.credentialOfferUri}`,
                "_blank"
              )
            }
            className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 mr-5"
          >
            Save to Affinidi Vault
          </button>

          <button
            type="submit"
            onClick={() => setissuanceResponse(undefined)}
            className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 mr-5"
          >
            Reject Credentials
          </button>

          <button
            type="submit"
            onClick={() => window.open("/", "_self")}
            className="inline-flex justify-center py-2 px-4 border border-transparent shadow-sm text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          >
            Return to Homepage
          </button>
        </div>
      )}
    </div>
  );
};

export default Credentials;

```

#### Step 6

Create `handleSubmit` function which invoke the Affinidi Credentials Issuance Service.

```javascript
const handleSubmit = async (event: React.FormEvent) => {
  event.preventDefault();
  console.log("Form submitted with credentials: ", credentials);

  const apiData = {
    credentialData: {
      email: credentials.email,
      name: credentials.name,
      phoneNumber: credentials.phoneNumber,
      dob: credentials.dob,
      gender: credentials.gender,
      address: credentials.address,
      postcode: credentials.postcode,
      city: credentials.city,
      country: credentials.country,
    },
    credentialTypeId: credentialTypeId,
    holderDid: session?.userId,
  };

  const response = await fetch("/api/credentials/issuance-start", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(apiData),
  });

  if (response.ok) {
    let DataResponse = await response.json();

    console.log("Credentials Issued");
    console.log(DataResponse);
    setissuanceResponse(DataResponse);
  } else {
    console.error("Failed to issue credentials");
  }
};
```

#### Step 7

Create Front-end API `src/pages/api/credentials/issuance-start.ts`

```javascript
import {
  StartIssuanceInput,
  StartIssuanceInputClaimModeEnum,
  StartIssuanceResponse,
} from "@affinidi-tdk/credential-issuance-client";
import { NextApiRequest, NextApiResponse } from "next";
import { CredentialsClient } from "../clients/credentials-client";

async function handler(
  req: NextApiRequest,
  res: NextApiResponse<StartIssuanceResponse>
) {
  const { holderDid, credentialTypeId, credentialData } = req.body;

  //   console.log("holderDid", holderDid);
  //   console.log("credentialTypeId", credentialTypeId);
  //   console.log("credentialData", credentialData);
  try {
    const apiData: StartIssuanceInput = {
      claimMode: StartIssuanceInputClaimModeEnum.TxCode,
      holderDid,
      data: [
        {
          credentialTypeId,
          credentialData: {
            ...credentialData,
            // Add any additional data here
          },
        },
      ],
    };
    //console.log("apiData", apiData);

    const issuanceResult = await CredentialsClient.IssuanceStart(apiData);

    // console.log("issuanceResult post backend call", issuanceResult);

    res.status(200).json(issuanceResult);
  } catch (error: any) {
    {
      response: error.response?.data ?? error;
    }
    ("Issuance failed");

    throw error;
  }
}

export default handler;

```

#### Step 8

create backend API Call `src/pages/api/clients/credentials-client.ts`

```javascript
import {
  apiGatewayUrl,
  passphrase,
  privateKey,
  projectId,
  tokenEndpoint,
  tokenId,
} from "@/lib/env";
import { AuthProvider } from "@affinidi-tdk/auth-provider";
import {
  IssuanceApi,
  Configuration as IssuanceConfiguration,
  StartIssuanceInput,
} from "@affinidi-tdk/credential-issuance-client";

const stats = {
  apiGatewayUrl: apiGatewayUrl,
  tokenEndpoint: tokenEndpoint,

  tokenId: tokenId,
  passphrase: passphrase,
  privateKey: privateKey,

  projectId: projectId,
};
const authProvider = new AuthProvider(stats);

export const CredentialsClient = {
  IssuanceStart: async (apiData: StartIssuanceInput) => {
    const api = new IssuanceApi(
      new IssuanceConfiguration({
        apiKey: authProvider.fetchProjectScopedToken.bind(authProvider),
        basePath: `${apiGatewayUrl}/cis`,
      })
    );

    const { data } = await api.startIssuance(projectId, apiData);
    console.log("startIssuance response", data);
    return data;
  },
};
```

#### Step 9

Try the App with Affinidi Login & Affinidi CIS

```sh
npm run dev
```

Open [http://localhost:3000/credentials](http://localhost:3000/credentials) with your browser to see the result.
