import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import IframeVideo from '@site/src/components/IframeVideo/index';
import SdkInstallation from './\_sdk-installation.mdx';
import QuickstartIntro from './\_quickstart-intro.mdx';
import QuickstartNadaComplete from './\_quickstart-write-nada-complete.mdx';
import QuickstartNada from './\_quickstart-write-nada.mdx';
import VenvSetup from './\_nada-venv-setup.mdx';

# JavaScript Developer Quickstart

Welcome to the JavaScript Quickstart. By the end of this guide, you will have:

1. Installed the Nillion SDK and set up your dev environment
2. Written, compiled, and tested your first nada program using the `nada` tool
3. Plugged your nada program into your first 'blind app' with starter code in cra-nillion
3. Connected your blind app to your local nillion-devnet to run it locally

Once you have finished, explore other demo pages in the `cra-nillion` repo to continue your Nillion developer journey!


## Install the Nillion SDK tools

<SdkInstallation/>

## Create a new folder for your quickstart

Create `quickstart`, a folder to hold your quickstart project - by the end of the quickstart, the folder will contain 2 subfolders - one for your nada project and one for the cloned cra-nillion starter repo

```
mkdir quickstart
```

## Create a new Nada project

```
cd quickstart
nada init nada_quickstart_programs
```

This will create a directory called `nada_quickstart_programs`, which is your Nada project. You can read more about [the file structure of this new Nada project here](/nada#create-a-new-project)


### Set up virtual environment

:::info

We're still in the JavaScript Quickstart, but in order to run the Nada program we're about to write, you'll need [python3](https://www.python.org/downloads/) version 3.11 or higher with a working [pip](https://pip.pypa.io/en/stable/getting-started/) installed

  - Confirm that you have python3 (version >=3.11) and pip installed
    ```
    python3 --version
    python3 -m pip --version
    ```
:::

Change directories into your new Nada project

```
cd nada_quickstart_programs
```

<VenvSetup/>

### Your first program
The code for the finished program is below - it is a simple program that has one party and adds two secret integer inputs together.

```python
from nada_dsl import *

def nada_main():

    party1 = Party(name="Party1")

    my_int1 = SecretInteger(Input(name="my_int1", party=party1))

    my_int2 = SecretInteger(Input(name="my_int2", party=party1))

    new_int = my_int1 + my_int2

    return [Output(new_int, "my_output", party1)]
```

Now we will write it from scratch, explaining how it works as we go. Once we have written the program, we will use the `nada` tool to run and test it.

1. From the `nada_quickstart_programs` Nada project, cd into the `src` folder and create a program file:
   ```bash
   cd src
   touch secret_addition.py
   ```
2. Write or copy the program above into this file

### Understanding the program you have just written

We will now go through the program slowly, and explain what is each part is doing. 

1. First we must import nada_dsl and create a function nada_main() - this function will contain our programs code.
   ```python
   from nada_dsl import *
   
   def nada_main():
   ```
2. Add a party
   ```python
   from nada_dsl import *
   
   def nada_main():
   
       party1 = Party(name="Party1")
   ```

   This is a one party program, however you can add multiple parties analogously, for example we could define `party2 = Party(name="Party2")`.
3. Add inputs to the program
   ```python
   from nada_dsl import *
   
   def nada_main():
   
       party1 = Party(name="Party1")
   
       my_int1 = SecretInteger(Input(name="my_int1", party=party1))
   
       my_int2 = SecretInteger(Input(name="my_int2", party=party1))
   ```
   
   This program has two inputs, both secret integers. Each input must have a `name` and a `party` associated to it. Currently in nada you can only compute on secret or public integers (and rationals by using the `nada-algebra` library).

4. Compute on the inputs
   ```python
   from nada_dsl import *
   
   def nada_main():
   
       party1 = Party(name="Party1")
   
       my_int1 = SecretInteger(Input(name="my_int1", party=party1))
   
       my_int2 = SecretInteger(Input(name="my_int2", party=party1))
   
       new_int = my_int1 + my_int2
   ```
   This performs a simple addition on the inputs. For all other built in operations available in nada, [see here](https://docs.nillion.com/nada-lang-operators).

5. Return the output of the program
   ```python
   from nada_dsl import *
   
   def nada_main():
   
       party1 = Party(name="Party1")
   
       my_int1 = SecretInteger(Input(name="my_int1", party=party1))
   
       my_int2 = SecretInteger(Input(name="my_int2", party=party1))
   
       new_int = my_int1 + my_int2
   
       return [Output(new_int, "my_output", party1)]
   ```
   To output the result of a program, we must provide a name - in this case my_output - and a party to whom the output is provided - in this case party1.

## Compile, run and test your program

Make sure you are in the `quickstart/nada_quickstart_programs` directory.

Now we will use the `nada` tool to compile, run and test the program we have just written. More information about the nada tool can be found [here](https://docs.nillion.com/nada).

1. Add your program to nada-project.toml

   For the nada tool to know about our program, we need to add the following to the to the nada-project.toml file.
   ```bash
   [[programs]]
   path = "src/secret_addition.py"
   name = "secret_addition"
   prime_size = 128
   ```

2. Build (compile) our program
   ```bash
   nada build
   ```

   This will compile all programs listed in the nada-project.toml file. You will see the binary files outputted in the nada-programs/target directory.

3. Generate test
   ```bash
   nada generate-test --test-name secret_addition_test secret_addition
   ```

   This uses the nada tool to generate a test, that will be stored in tests. Here secret_addition_test is the name of the test, and secret_addition is the name of the program we want to test. You will notice that the test file (`tests/secret_addition_test.yaml`) is automatically populated with `3`s everywhere by default. Later, for the test to pass, we will have to change the output from `3` to the correct output.

4. Run the program
   ```bash
   nada run secret_addition_test
   ```
   
   Now we run the program. This uses the inputs defined in the test file (tests/secret_addition_test.yaml) and runs the program and prints the result. Make note of the result, we will need it next.

5. Test the program
   ```bash
   nada test secret_addition_test
   ```
   
   Finally, we test the program. If you run the above command without altering the default values (`3`s) in the test file (`tests/secret_addition_test.yaml`), the test will fail. Modify values in the test file and re-test the program.

Well done! You've just written your first Nada program! Now we'll hook this up to a blind app, which will be able to compute with the `secret_addition` Nada program on secret inputs.


## Clone the CRA-Nillion JavaScript starter repo

The [cra-nillion Starter Repo](https://github.com/NillionNetwork/cra-nillion) repo is a Create React App with has everything you need to start building your blind app. Clone the repo:

Make sure you are in the root of the `quickstart` directory.

```bash
git clone https://github.com/NillionNetwork/cra-nillion.git
```

### Install repo dependencies and run the starter

:::info

Before you use [cra-nillion](https://github.com/NillionNetwork/cra-nillion), check that you have [Node (>= v18.17)](https://nodejs.org/en/download/) installed by running
    ```
    node -v
    ```
:::

```
cd cra-nillion
npm i 
npm start
```

Open http://localhost:8080/ to see your cra-nillion starter app running locally at port 8080

![CRA nillion no cluster](/img/cra-nillion-no-cluster.png)

For this Quickstart, we'll focus on the Nillion Operations page and the Nillion Blind Computation Demo page. 

## Connect the blind app to nillion-devnet 

In the screenshot of cra-nillion, you'll notice that cluster id and other configuration variables needed to connect to the Nillion Network are not set, so it's not possible to connect NillionClient. 

### Spin up a local Nillion devnet

Open a second terminal and run the devnet using any seed (the example uses "my-seed") so the cluster id, websockets, and other environment variables stay constant even when you restart nillion-devnet.

```shell
nillion-devnet --seed my-seed
```

You will see an output like this:

```
nillion-devnet --seed my-seed
ℹ️ cluster id is 222257f5-f3ce-4b80-bdbc-0a51f6050996
ℹ️ using 256 bit prime
ℹ️ storing state in /var/folders/1_/2yw8krkx5q5dn2jbhx69s4_r0000gn/T/.tmpU00Jbm (62.14Gbs available)
🏃 starting nilchain node in: /var/folders/1_/2yw8krkx5q5dn2jbhx69s4_r0000gn/T/.tmpU00Jbm/nillion-chain
⛓  nilchain JSON RPC available at http://127.0.0.1:48102
⛓  nilchain gRPC available at localhost:26649
🏃 starting node 12D3KooWMGxv3uv4QrGFF7bbzxmTJThbtiZkHXAgo3nVrMutz6QN
⏳ waiting until bootnode is up...
🏃 starting node 12D3KooWKkbCcG2ujvJhHe5AiXznS9iFmzzy1jRgUTJEhk4vjF7q
🏃 starting node 12D3KooWMgLTrRAtP9HcUYTtsZNf27z5uKt3xJKXsSS2ohhPGnAm
👛 funding nilchain keys
📝 nillion CLI configuration written to /Users/steph/Library/Application Support/nillion.nillion/config.yaml
🌄 environment file written to /Users/steph/Library/Application Support/nillion.nillion/nillion-devnet.env
```

Copy the path printed after "🌄 environment file written to" and open the file

```
vim "/Users/steph/Library/Application Support/nillion.nillion/nillion-devnet.env"
```

This file has the nillion-devnet generated values for cluster id, websocket, json rpc, and private key. You'll need to put these in your local .env in one of the next steps so that your cra-nillion demo app connects to the nillion-devnet.

Keep the nillion-devnet running in this terminal.

### Create .env file

Make sure you are in the `quickstart/cra-nillion` directory.

Copy the up the .env.example file to a new .env and set up these variables to match the nillion environment file. 

```shell
cp .env.example .env
```

Update your newly created .env with environment variables outout in your terminal by nillion-devnet

```
REACT_APP_NILLION_CLUSTER_ID=
REACT_APP_NILLION_BOOTNODE_WEBSOCKET=
REACT_APP_NILLION_NILCHAIN_JSON_RPC=
REACT_APP_NILLION_NILCHAIN_PRIVATE_KEY=
REACT_APP_API_BASE_PATH=/nilchain-proxy
```

Restart the cra-nillion app process

```
npm start
```

Now the Cluster ID field should be populated with the nillion-devnet cluster id value you set in REACT_APP_NILLION_CLUSTER_ID.

![CRA nillion with cluster](/img/cra-nillion-with-cluster.png)

## Try out the Operations Page

1. Generate User Key - generates a new user key / user id pair
2. Connect with User Key - sets the user key and connects to NillionClient via the Nillion JavaScript Client
3. Hide Nillion User Key and Node Key Connection Info - toggle button to show/hide user and node key options
4. Perform Nillion Operations

To perform an operation (store secret, retrieve secret, update secret, store program, compute), you follow the same pattern: 
  1. Get quote for the operation 
  2. Pay quote for the operation and get a payment receipt. On your local nillion-devnet, payments for operations are sent to the local nilchain at REACT_APP_NILLION_NILCHAIN_JSON_RPC are funded by REACT_APP_NILLION_NILCHAIN_PRIVATE_KEY
  3. Perform the operation with the payment receipt as a parameter

    ![CRA nillion operations](/img/cra-nillion-operations.png)

## Hook up your secret_addition.py nada program to your first blind app

Now that you understand how Nillion operations work, let's update the Nillion Blind Computation Demo page to use the Nada program you created.

Navigate to the Blind Computation Demo page: http://localhost:8080/compute

The code for Blind Computation Demo page [lives in ComputePage.tsx](https://github.com/NillionNetwork/cra-nillion/blob/main/src/ComputePage.tsx)

```
const outputName = 'my_output';
const partyName = 'Party1';
```

Notice that the ComputePage sets `outputName` which matches the the output name set in secret_addition.py. The ComputePage sets `partyName` which matches the the party name set in secret_addition.py. There are 2 `StoreSecretForm` components on ComputePage, with secretName set to `my_int1` and `my_int2` which matches the the secret names set in secret_addition.py.

<Tabs>
<TabItem value="helpers" label="secret_addition.py" default>
```
from nada_dsl import *

def nada_main():

    party1 = Party(name="Party1")

    my_int1 = SecretInteger(Input(name="my_int1", party=party1))

    my_int2 = SecretInteger(Input(name="my_int2", party=party1))

    new_int = my_int1 + my_int2

    return [Output(new_int, "my_output", party1)]
```
</TabItem>

<TabItem value="compute" label="ComputePage.tsx" >
```ts reference showGithubLink
https://github.com/NillionNetwork/cra-nillion/blob/main/src/ComputePage.tsx
```
</TabItem>

</Tabs>

### Update programName

In order to run blind computation on your `secret_addition.py` Nada program, you'll need to make a few updates:

1. Open a new terminal and navigate to the root `quickstart` folder. List the contents of the folder
```
ls
```
You should see cra-nillion and nada_quickstart_programs folders.

2. Copy your secret_addition.py and secret_addition.nada.bin files nada_quickstart_programs into cra-nillion

```
cp nada_quickstart_programs/src/secret_addition.py cra-nillion/public/programs
cp nada_quickstart_programs/target/secret_addition.nada.bin cra-nillion/public/programs
```

Now your cra-nillion app can use the nada program and the nada program binaries in store program operations.

3. Update programName to `secret_addition` so the cra-nillion repo reads your Nada program.

```ts reference showGithubLink
https://github.com/NillionNetwork/cra-nillion/blob/main/src/ComputePage.tsx#L13
```

## Run the Blind Computation Demo

Go back to the Blind App on http://localhost:8080/compute and run through the steps on the page to test out the full blind computation flow.

## Keep exploring

You've successfully build your first blind app by writing a Nada program, storing the program on the network, storing secrets on the network, and running compute against secrets. Keep exploring by

- reading about [Nillion concepts](/concepts) and the [Nada Language](nada-lang)
- learning how to interact with and manage programs, secrets, and permissions on the Nillion Network with [Nillion Client](/js-client)
- challenging yourself to create a page that solves the [millionaires problem](/multi-party-computation#classic-scenario-the-millionaires-problem)

:::tip

Open the Nillion [JavaScript Client Reference](https://nillion.pub/nillion-js-reference/) doc in another tab to search for available Nillion Client classes while working with cra-nillion.

:::