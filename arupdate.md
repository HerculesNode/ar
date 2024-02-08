#!/bin/bash
echo -e ''
curl -s https://scannerx.net/setup/logohercul.txt | bash && sleep 3
echo -e ''
GREEN="\e[32m"
NC="\e[0m"


binaries () {
echo -e '\e[0;35m'
if [ ! $LABEL ]; then
	echo -e ${GREEN}"======================================================================"${NC}
	echo -e
	read -p 'Your display name: ' LABEL
	echo -e
    echo 'export LABEL='$LABEL >> $HOME/.bash_profile
	sleep 1
   
fi

   
}

api_request () {
echo -e '\e[0;35m'
if [ ! $FQDN ]; then
		echo -e ${GREEN}"======================================================================"${NC}
		echo -e
		read -p "Your web adress ex: scannerx.net: " FQDN
		echo -e
		echo 'export FQDN='\"${FQDN}\" >> $HOME/.bash_profile
        
fi
        echo -e "${GREEN}"
if [ ! $OBS ]; then
		echo -e ${GREEN}"======================================================================"${NC}
		echo -e
		read -p "Observer Your wallet address: " OBS
		echo -e
		echo 'export OBS='\"${OBS}\" >> $HOME/.bash_profile
		echo -e ${GREEN}"======================================================================"${NC}
        echo -e  '\e[0m'
fi

}



services () {

    sudo tee /root/testnet-contract/tools/update.ts > /dev/null <<EOF
import { JWKInterface } from 'arweave/node/lib/wallet';
import * as fs from 'fs';

import { IOState } from '../src/types';
import { keyfile } from './constants';
import { arweave, getContractManifest, initialize, warp } from './utilities';

/* eslint-disable no-console */
// This script will update the settings for a gateway that is already joined to the network
// Only the gateway's wallet owner is authorized to adjust these settings
(async () => {
  initialize();

  // the friendly label for this gateway
  const label = '$LABEL';

  // the fully qualified domain name for this gateway eg. arweave.net
  const fqdn = '$FQDN';

  // uncomment the below settings and update as needed
  // the port used for this gateway eg. 443
  // const port = 443

  // the application layer protocol used by this gateway eg http or https
  // const protocol = 'https'

  // an optional gateway properties file located at this Arweave transaction id eg.
  // const properties = 'FH1aVetOoulPGqgYukj0VE0wIhDy90WiQoV3U2PeY44'

  // an optional, short note to further describe this gateway and its status
  // const note = 'Give me feedback about this gateway at my Xwitter @testgatewayguy'

  // The observer wallet public address eg.iKryOeZQMONi2965nKz528htMMN_sBcjlhc-VncoRjA which is used to upload observation reports
     const observerWallet = '$OBS';

  // Get the key file used for the distribution
  const wallet: JWKInterface = JSON.parse(
    process.env.JWK ? process.env.JWK : fs.readFileSync(keyfile).toString(),
  );

  // gate the contract txId
  const arnsContractTxId =
    process.env.ARNS_CONTRACT_TX_ID ??
    'bLAgYxAdX2Ry-nt6aH2ixgvJXbpsEYm28NgJgyqfs-U';

  // wallet address
  const walletAddress = await arweave.wallets.getAddress(wallet);

  // get contract manifest
  const { evaluationOptions = {} } = await getContractManifest({
    contractTxId: arnsContractTxId,
  });

  // Read the ANT Registry Contract
  const contract = await warp
    .contract<IOState>(arnsContractTxId)
    .connect(wallet)
    .setEvaluationOptions(evaluationOptions)
    .syncState(\`https://api.arns.app/v1/contract/\${arnsContractTxId}\`, {
      validity: true,
    });

  // Include any settings as needed below
  const writeInteraction = await contract.writeInteraction(
    {
      function: 'updateGatewaySettings',
		 label,
		 fqdn,
		 observerWallet,
      // port,
      // protocol,
      // properties,
      // note
    },
    {
      disableBundling: true,
    },
  );

  console.log(
    \`\${walletAddress} successfully updated gateway settings with TX id: \${writeInteraction?.originalTxId}\`,
  );
})();
EOF
}




jhome () {

  cd $HOME/testnet-contract
  yarn ts-node tools/update.ts
   
  
}


info () {
  source $HOME/.profile
  source $HOME/.bash_profile
  echo -e ${GREEN}"======================================================================"${NC}
  echo -e '\e[1;32mThe update process is finished. Please check this address.\e[0m'
  echo -e 'https://viewblock.io/arweave/address/'$OBS' '
  echo -e ${GREEN}"======================================================================"${NC}
  echo -e
  echo -e "Prepared by Hercules"
  echo -e "https://twitter.com/HerculesNode"
  echo -e "https://github.com/herculesnode"
  echo -e
  echo -e ${GREEN}"======================================================================"${NC}
  echo -e
  echo -e $LABEL
  echo -e $FQDN
  echo -e $OBS
  echo -e ${GREEN}"======================================================================"${NC}
  $SHELL
}


PS3="What do you want?: "
select opt in İnstall  quit; 
do

  case $opt in
    İnstall)
    echo -e '\e[1;32mThe installation process begins...\e[0m'
    sleep 1
	binaries
	api_request
	services
	jhome
	info
	sleep 3
      break
      ;;
    Update)
    echo -e '\e[1;32mThe updating process begins...\e[0m'
    echo -e ''
    echo -e '\e[1;32mSoon...'
    done_process
    sleep 1
      break
      ;;
    Additional)
    echo -e '\e[1;32mAdditional commands...\e[0m'
    echo -e ''
    echo -e '\e[1;32mSoon...'
    sleep 1
      ;;
    quit)
    echo -e '\e[1;32mexit...\e[0m' && sleep 1
      break
      ;;
    *) 
      echo "Invalid $REPLY"
      ;;
  esac
done
