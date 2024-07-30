## Introduction

This is a [Next.js](https://nextjs.org/) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app).

```sh
npx create-next-app
```

## Getting Started

First, run the development server:

```sh
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `pages/index.tsx`. The page auto-updates as you edit the file.

## Affinidi Login

This document illustrate step by step guide to add Affinidi Login with `nextjs` Framework with `nextauthjs` Library

```bash
npm install next-auth
```

#### Step 1

Add CSS with for Affinidi login button [style Guide](https://docs.affinidi.com/docs/affinidi-login/button-styleguide/) `src/styles/globals.css`

for eg: for Large button use below code snippet

```
.affinidi-login-dark-l {
  border: 0;
  width: 224px;
  height: 56px;
  display: flex;
  flex-direction: row;
  justify-content: center;
  box-sizing: border-box;
  align-items: center;
  gap: 12px;
  padding: 12px 32px;
  object-fit: contain;
  border-radius: 48px;
  background: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" width="29" height="24" viewBox="0 0 29 24" fill="none"><path d="M3.22 20.281A11.966 11.966 0 0 0 11.904 24c3.415 0 6.498-1.428 8.683-3.719H3.219h.001zM20.588 6.762H1.106A11.933 11.933 0 0 0 0 10.48h20.588V6.762zM20.586 3.719A11.966 11.966 0 0 0 11.902 0 11.966 11.966 0 0 0 3.22 3.719h17.367zM20.588 13.521H0c.167 1.319.548 2.57 1.106 3.719h19.482v-3.718zM22.703 6.762c.558 1.148.94 2.4 1.106 3.718h4.78V6.762h-5.886z" fill="%23040822"/><path d="M28.586 20.281h-8V24h8V20.28zM22.703 17.24h5.886v-3.718h-4.78a11.93 11.93 0 0 1-1.106 3.718zM28.586 0h-8v3.719h8V0z" fill="%23040822"/><path d="M23.807 10.48A11.931 11.931 0 0 0 22.7 6.76a12.012 12.012 0 0 0-2.115-3.041v16.563A12.045 12.045 0 0 0 22.7 17.24 11.932 11.932 0 0 0 23.9 12c0-.516-.031-1.023-.094-1.522v.001z" fill="%231D58FC"/></svg>')
    no-repeat 25px center;
  background-color: #ffffff;
  color: #000;
  padding-left: 60px;

  flex-grow: 0;
  font-family: Figtree;
  font-size: 18px;
  font-weight: 600;
  font-stretch: normal;
  font-style: normal;
  line-height: 1.22;
  letter-spacing: 0.6px;
}

.affinidi-login-dark-l:hover {
  background-color: #e6e6e9;
}

.affinidi-login-dark-l:active {
  background-color: #cdced3;
}

.affinidi-login-dark-l-loading {
  background: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 20 20" fill="none"><g clip-path="url(%230e2gocc7wa)"><path fill-rule="evenodd" clip-rule="evenodd" d="M9.727 4.545v2.728l3.864-3.637L9.727 0v2.727C5.457 2.727 2 5.982 2 10c0 1.427.444 2.755 1.198 3.873l1.41-1.327A5.09 5.09 0 0 1 3.932 10c0-3.01 2.598-5.455 5.795-5.455zm6.53 1.582-1.41 1.328c.425.763.676 1.627.676 2.545 0 3.01-2.599 5.454-5.796 5.454v-2.727l-3.863 3.637L9.727 20v-2.727c4.27 0 7.727-3.255 7.727-7.273a6.904 6.904 0 0 0-1.197-3.873z" fill="%231D2138"/></g><defs><clipPath id="0e2gocc7wa"><path fill="%23fff" d="M0 0h20v20H0z"/></clipPath></defs></svg>')
    no-repeat center;
  background-color: #e6e6e9;
}

.affinidi-login-dark-l:disabled {
  background: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" width="30" height="24" viewBox="0 0 30 24" fill="none"><path d="M3.927 20.281A11.966 11.966 0 0 0 12.61 24c3.416 0 6.499-1.428 8.684-3.719H3.926h.001zM21.295 6.762H1.813A11.933 11.933 0 0 0 .707 10.48h20.588V6.762zM21.293 3.719A11.967 11.967 0 0 0 12.609 0a11.966 11.966 0 0 0-8.683 3.719h17.367zM21.295 13.521H.707c.167 1.319.548 2.57 1.106 3.719h19.482v-3.718zM23.41 6.762c.558 1.148.94 2.4 1.106 3.718h4.78V6.762H23.41z" fill="%23fff"/><path d="M29.293 20.281h-8V24h8V20.28zM23.41 17.24h5.886v-3.718h-4.78a11.933 11.933 0 0 1-1.106 3.718zM29.293 0h-8v3.719h8V0z" fill="%23fff"/><path d="M24.514 10.48a11.934 11.934 0 0 0-1.106-3.72 12.017 12.017 0 0 0-2.115-3.041v16.563a12.05 12.05 0 0 0 2.115-3.042 11.935 11.935 0 0 0 1.2-5.24c0-.516-.031-1.023-.094-1.522v.001z" fill="%23fff"/></svg>')
    no-repeat 25px center;
  background-color: #e6e6e9;
  color: #ffffff;
}
```

#### Step 2

Import next package and Update `src/pages/index.tsx` with Affinidi Login Buttion and `handleLogin` to initiate Login

for eg

```javascript
import Image from "next/image";
import { Inter } from "next/font/google";
import { useEffect, useState } from "react";
import { getSession, signOut } from "next-auth/react";
import { Session } from "next-auth";
// import { clientLogin } from "@/lib/auth/client-login";
```

```javascript
export default function Home() {
    const [session, setsession] = useState<Session>();

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

  async function handleLogin() {
   // await clientLogin();
  }
  return (
    <main
      className={`flex min-h-screen flex-col items-center justify-between p-24 ${inter.className}`}
    >
      <div className="z-10 max-w-5xl w-full items-center justify-between font-mono text-sm lg:flex">
        <a href="/">
          <p className="fixed left-0 top-0 flex w-full justify-center border-b border-gray-300 bg-gradient-to-b from-zinc-200 pb-6 pt-8 backdrop-blur-2xl dark:border-neutral-800 dark:bg-zinc-800/30 dark:from-inherit lg:static lg:w-auto  lg:rounded-xl lg:border lg:bg-gray-200 lg:p-4 lg:dark:bg-zinc-800/30">
            Affinidi Login Demo
          </p>
        </a>

        <div className="fixed bottom-0 left-0 flex h-48 w-full items-end justify-center bg-gradient-to-t from-white via-white dark:from-black dark:via-black lg:static lg:size-auto lg:bg-none">
          {!session && (
            <button className="affinidi-login-dark-l" onClick={handleLogin}>
              Affinidi Login
            </button>
          )}
          {session && (
            <button className="affinidi-login-dark-l" onClick={() => signOut()}>
              Logout
            </button>
          )}
        </div>
      </div>

      <div className="relative z-[-1] flex place-items-center before:absolute before:h-[300px] before:w-full before:-translate-x-1/2 before:rounded-full before:bg-gradient-radial before:from-white before:to-transparent before:blur-2xl before:content-[''] after:absolute after:-z-20 after:h-[180px] after:w-full after:translate-x-1/3 after:bg-gradient-conic after:from-sky-200 after:via-blue-200 after:blur-2xl after:content-[''] before:dark:bg-gradient-to-br before:dark:from-transparent before:dark:to-blue-700 before:dark:opacity-10 after:dark:from-sky-900 after:dark:via-[#0141ff] after:dark:opacity-40 sm:before:w-[480px] sm:after:w-[240px] before:lg:h-[360px]">
        {!session && (
          <Image
            className="relative dark:drop-shadow-[0_0_0.3rem_#ffffff70] dark:invert"
            src="/next.svg"
            alt="Next.js Logo"
            width={180}
            height={37}
            priority
          />
        )}
        {session && (
          <div className="flex flex-col items-center justify-center">
            <h1 className="text-2xl font-bold">
              Welcome {session.user?.email}
            </h1>
            &nbsp;
            <p className="text-lg">Userid : {session.userId}</p>
            &nbsp;
            <p>Idtoken : {session.idToken}</p>
            &nbsp;
            <p className="text-lg">Accesstoken : {session.accessToken}</p>
          </div>
        )}
      </div>
    </main>
  );
}
```

#### Step 3

create client login `src/lib/auth/client-login.ts` which is being called from `handleLogin` function

```javascript
import { signIn } from "next-auth/react";

export async function clientLogin() {
  await signIn("affinidi");
}
```

#### Step 4

Create API to call Login `src/pages/api/auth/[...nextauth].ts`

```javascript
import { authOptions } from "@/lib/auth/auth-options";
import NextAuth from "next-auth";

export default NextAuth(authOptions);
```

#### Step 5

Create Auth Option for Login `src/lib/auth/auth-options.ts`

```javascript
import { NextAuthOptions } from "next-auth";

import { provider, PROVIDER_ATTRIBUTES_KEY } from "./auth-provider";
import { UserInfo } from "@/types/types";

export const authOptions: NextAuthOptions = {
  // debug: true,
  session: { strategy: "jwt" },
  providers: [provider],
  callbacks: {
    // checks whether user is allowed to sign in
    async signIn({ account }) {
      return Boolean(
        account?.provider === provider.id &&
          account.access_token &&
          account.id_token
      );
    },

    // "account" and "profile" are only passed the first time this callback is called on a new session, after the user signs in
    // this defines how JWT is generated and is then used in session() callback as "token"
    async jwt({ token, account, profile }) {
      const profileItems = (profile as any)?.[PROVIDER_ATTRIBUTES_KEY];
      if (profile && profileItems) {
        let userDID: string;
        let user: UserInfo = {};
        userDID = profileItems.find(
          (item: any) => typeof item.did === "string"
        )?.did;
        user.email = profileItems.find(
          (item: any) => typeof item.email === "string"
        )?.email;
        //  user.phoneNumber = profileItems.find(
        //    (item: any) => typeof item.phoneNumber === "string"
        //  )?.phoneNumber;

        token = {
          ...token,
          user,
          ...(userDID && { userId: userDID }),
        };
      }
      if (account) {
        token = {
          ...token,
          ...(account?.access_token && { accessToken: account.access_token }),
          ...(account?.id_token && { idToken: account.id_token }),
        };
      }
      // Here you also have access to account.access_token and account.id_token

      return token;
    },

    // session is persisted as an HttpOnly cookie
    async session({ session, token }) {
      return {
        ...session,
        ...(token.user && { user: { ...session.user, ...token.user } }),
        ...(token.userId && { userId: token.userId }),
        ...(token.accessToken && { accessToken: token.accessToken }),
        ...(token.idToken && { idToken: token.idToken }),
      };
    },
  },
};

```

#### Step 6

Create Affinidi Auth provider `src/lib/auth/auth-provider.ts`

```javascript
import { Provider } from "next-auth/providers/index";
import { providerClientId, providerClientSecret, providerIssuer } from "../env";

export const PROVIDER_ATTRIBUTES_KEY = "custom";

export const provider: Provider = {
  id: "affinidi",
  name: "Affinidi",
  clientId: providerClientId,
  clientSecret: providerClientSecret,
  type: "oauth",
  wellKnown: `${providerIssuer}/.well-known/openid-configuration`,
  authorization: {
    params: {
      prompt: "login",
      scope: "openid offline_access",
    },
  },
  client: {
    token_endpoint_auth_method: "client_secret_post",
  },
  idToken: true,
  profile(profile) {
    return {
      id: profile.sub,
      email: profile.custom?.find((i: any) => typeof i.email === "string")
        ?.email,
    };
  },
};
```

#### Step 7

Create types for `next-auth` and `next-auth/jwt` in `src/types/next-auth.d.ts`

```javascript
import { DefaultSession, DefaultUser } from "next-auth";
import { DefaultJWT } from "next-auth/jwt";

import { UserInfo } from "src/types/types";

declare module "next-auth" {
  interface Session extends DefaultSession {
    userId: string;
    user?: UserInfo;
    accessToken: string;
    idToken: string;
  }
}

declare module "next-auth/jwt" {
  interface JWT extends DefaultJWT {
    userId: string;
    user?: UserInfo;
    accessToken: string;
    idToken: string;
  }
}

```

#### Step 8

Create Response Types in `src/types/types.ts`

```javascript
export type ResponseError = {
  message: string,
};

export type UserInfo = {
  email?: string,
  phoneNumber?: string,
};
```

#### Step 9

Create Env variables in `src/lib/env.ts`

```javascript
export const providerClientId = process.env.PROVIDER_CLIENT_ID!;
export const providerClientSecret = process.env.PROVIDER_CLIENT_SECRET!;
export const providerIssuer = process.env.PROVIDER_ISSUER!;
```

#### Step 10

create `.env` variables

```
PROVIDER_CLIENT_ID = "<AUTH.CLIENT_ID>";
PROVIDER_CLIENT_SECRET = "<AUTH.CLIENT_SECRET>";
PROVIDER_ISSUER = "<AUTH.ISSUER>";
```

#### Step 11

Create Login Configuration using [Affinidi CLI](https://docs.affinidi.com/dev-tools/affinidi-cli/manage-login/#affinidi-login-create-config) or [Affinidi Portal](https://portal.affinidi.com/affinidiLogin)

Redirect URL : `http://localhost:3000/api/auth/callback/affinidi`

More details here in [Affinidi Documentation](https://docs.affinidi.com/labs/languages/affinidi-login-basic/#create-login-configuration)

#### Step 12

Try the App with Affinidi Login

```sh
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.
