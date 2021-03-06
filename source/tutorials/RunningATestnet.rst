*****************
Running a Testnet
*****************

The following instructions covers the setup of a witness in Ubuntu 14.04. The
instructions are specific for the Test 3 release, but should be easily ported to
other releases.

You can get latest version with chain id, genesis, and chain id from `github`_.

.. _github: https://github.com/cryptonomex/graphene/releases

.. note:: Nodes might gone down as everyone is testing.

##Installation of dependencies
Extracted from instructions here:
https://github.com/cryptonomex/graphene/wiki/build-ubuntu, but limited only for
witness / cli build.

If you already have done your installation move to next step::

    sudo apt-get update

Installation of development tools::

    sudo apt-get install gcc-4.9 g++-4.9 cmake make libbz2-dev libdb++-dev libdb-dev libssl-dev openssl libreadline-dev autoconf libtool git

If you cannot install gcc-4.9, you will need to add this repository before hand and try again.::

    sudo add-apt-repository ppa:ubuntu-toolchain-r/test

###Installation of BOOST

::
    BOOST_ROOT=$HOME/opt/boost_1_57_0
    sudo apt-get update
    sudo apt-get install autotools-dev build-essential g++ libbz2-dev libicu-dev python-dev
    wget -c 'http://sourceforge.net/projects/boost/files/boost/1.57.0/boost_1_57_0.tar.bz2/download' -O boost_1_57_0.tar.bz2
    [ $( sha256sum boost_1_57_0.tar.bz2 | cut -d ' ' -f 1 ) == "910c8c022a33ccec7f088bd65d4f14b466588dda94ba2124e78b8c57db264967" ] || ( echo 'Corrupt download' ; exit 1 )
    tar xjf boost_1_57_0.tar.bz2
    cd boost_1_57_0/
    ./bootstrap.sh "--prefix=$BOOST_ROOT"
    ./b2 install [/code]

##Git  checkout and build
Ensure your boost path is correct::

    BOOST_ROOT=$HOME/opt/boost_1_57_0

Check out and build::

    cd ~
    git clone https://github.com/cryptonomex/graphene.git
    cd graphene

**Check out test 3 (specific to this chain)**

::
    git checkout test3

Update submodules and build make file::
 
    git submodule update --init --recursive
    cmake -DBOOST_ROOT="$BOOST_ROOT" -DCMAKE_BUILD_TYPE=Debug .
    make 


##Setup witness / import balances

_Navigate to the witness directory_::

    cd ~/graphene/programs/witness_node

_Download genesis_ 
**Path specific for Test 3**

::
     wget https://github.com/cryptonomex/graphene/releases/download/test3/sep-18-testnet-genesis.json

_Start a new terminal screen_

::
     screen

_Run the witness_
**Current nodes for test 3 (replace for other tests)**

::
    ./witness_node --rpc-endpoint "127.0.0.1:8090"  --genesis-json sep-18-testnet-genesis.json -d test_net_3 -s  "104.236.118.105:1776"

.. note::
 `-d` parameter is for the directory you want the witness data to be stored
 `-s` is the node you want to connect

If you have problems, you might need to put the whole path for the genesis::

    ./witness_node --rpc-endpoint "127.0.0.1:8090"  --genesis-json ~/graphene/programs/witness-node/sep-18-testnet-genesis.json -d test_net_3 -s "104.236.118.105:1776" 

_Detach from screen_

::
     Ctrl A Ctrl D

_Extract your wif keys for user and balances as per xeroc's instructions_
:doc:`Howto-become-an-active-witness-in-BitShares-2.0`

_Navigate to cli_wallet_

::
    cd ~/graphene/programs/cli_wallet

_Run cli_  
**Current chain id for test 3**

::
     ./cli_wallet -w test_wallet  --chain-id 0f8b631d7a9dfebf16d6776fab96b629a14429762bf9c3eb95db1e4e4af637a4

.. note:: `-w` is your directory wallet

_Setup witness as per xerocs instructions_ 

:doc:`Howto-become-an-active-witness-in-BitShares-2.0`

.. note:: Remember to copy your keys, witness id
          Note you need to wait for a maintenance period to be voted in

_Exit_

_Go back to your witness screen_

::
  screen -r 

_Exit your witness_

::
    ctrl c

_Restart with parameters to start block producing (block producing needs your witness id and private keys)_ **Current nodes for test 3 (replace for other tests)**

::
    ./witness_node --rpc-endpoint "127.0.0.1:8090"  --genesis-json sep-18-testnet-genesis.json -d test_net_3 -s  "104.236.118.105:1776"  --witness-id '"1.6.5156"' --private-key '["GPH6JhL..your.signing.key..bc5mWyCvERV3coy","5K..your.secret..a"]'

_See your witness producing blocks and 
you can Ctrl A Ctrl D to detach from screen._

##Tips

If you end up in a fork, or your blockchain gets corrupted it takes a long time to replay blockchain.

>When I am synced, I shutdown with C-c (it should shutdown in a clean way) and I copy the blockchain folder as       backup (if it shutdown without errors).
>Everytime my blockchain is corrupted, I remove the blockchain folder and I copy with the backup one and restart    the witness.
>Finally I backup the blockchain folder every day.

>spartako

##Credits
xeroc, puppies, abit, clayop, betax, maqifrnswa, lafona, IHashfury, Riverhead,
testz,  cryptosile, Thom, spartako
