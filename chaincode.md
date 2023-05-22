## chaincode

### 环境

[fabric-contract-api - npm (npmjs.com)](https://www.npmjs.com/package/fabric-contract-api)

```shell
npm install --save fabric-contract-api
```

```shell
npm install --save fabric-shim
#应该也不是必须的，可以二选一
```

### 文件

index.js

chaincode.js

package.json

#### index.js

```js
// index.js
'use strict';

const UpdateValues = require('./chaincode')
module.exports.contracts = [UpdateValues];

//其中UpdateValues可以自己取名，注意./chaincode路径一定要正确
```

### chaincode.js

```js
// chaincode.js 范例，可以不用初始化函数
'use strict';

// SDK Library to asset with writing the logic
const { Contract } = require('fabric-contract-api');

// Business logic (well just util but still it's general purpose logic)
const util = require('util');

/**
 * Support the Updating of values within the SmartContract
 */
class UpdateValuesContract extends Contract

  constructor(){
	   	super('UpdateValuesContract');
	}

	async transactionA(ctx, newValue) {
		// retrieve existing chaincode states
		let oldValue = await ctx.stub.getState(key);

		await ctx.stub.putState(key, Buffer.from(newValue));

		return Buffer.from(newValue.toString());
	}

	async transactionB(ctx) {
	  //  .....
	}

};

// UpdateValuesContract与类名对应
module.exports = UpdateValuesContract

```

### package.json

```json
"scripts": {
    "start": "fabric-chaincode-node start"
  },
```

##### 参考范例--chaincode：usercc.js

```js
/*
* Licensed under the Apache License, Version 2.0 (the "License");
* you may not use this file except in compliance with the License.
* You may obtain a copy of the License at
*
* http://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

'use strict';

const { Contract } = require('fabric-contract-api');

class UserContract extends Contract {

    async init(){
        return;
    }

    async initLedger(ctx) {
        console.info('============= START : Initialize Ledger ===========');
        
        console.info('============= END : Initialize Ledger ===========');
    }

    async queryUser(ctx, UserPID) {
        const userAsBytes = await ctx.stub.getState(UserPID); 
        if (!userAsBytes || userAsBytes.length === 0) {
            throw new Error(`This User does not exist`);
        }
        console.log(userAsBytes.toString());
        return userAsBytes.toString();
    }

    async createUser(ctx, UserPID, Role, Org) {
        console.info('============= START : Create User ===========');
        const userAsBytes = await ctx.stub.getState(UserPID);
        if (!userAsBytes || userAsBytes.length === 0) {
            const User = {
            Role,
            Org,
            Numbers: [],
        };

        await ctx.stub.putState(UserPID, Buffer.from(JSON.stringify(User)));
        console.info('============= END : Create User ===========');
        }else{
            throw new Error(`This User already exists`);
        }
    }

    async AddNumber(ctx, Number, UserPID) {
        console.info('============= START : Add Number ===========');

        const UserAsBytes = await ctx.stub.getState(UserPID); 
        if (!UserAsBytes || UserAsBytes.length === 0) {
            throw new Error(`This User does not exist`);
        }
        const User = JSON.parse(UserAsBytes.toString());
        User.Numbers.push(Number);

        await ctx.stub.putState(UserPID, Buffer.from(JSON.stringify(User)));
        console.info('============= END : Add Number ===========');
    }
}

module.exports = UserContract;

```

