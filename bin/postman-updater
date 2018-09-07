#!/usr/bin/env bash

# Set python's encoding.
PYTHONIOENCODING=utf8;

# Helper vars
_TRUE_="true";
_FALSE_="false";

LOCATION="/opt/Postman"  # Default install/update location
VERSION="latest"  # Default to latest version

function __debug {
    if [[ ${PM_DEBUG} == "true" ]]; then
        echo "[*] [DEBUG] " $@ >&2;
    fi
}

function usage {
    echo "Usage:";
    echo "    postman-updater -h                                  Display this help message.";
    echo "    postman-updater check [-l INSTALL_DIRECTORY]        Check for updates.";
    echo "    postman-updater update [-l INSTALL_DIRECTORY]       Update to the latest version.";
    echo "    postman-updater install [-l INSTALL_DIRECTORY]      Install the latest version of Postman.";
    exit 0;
}

function is_installed {
    local LOCATION=$1;
    local PKG_JSON_LOCATION=${LOCATION}/app/resources/app/package.json;

    __debug "Checking if Postman is already installed in location: " ${LOCATION};

    if [[ -f ${PKG_JSON_LOCATION} ]]; then
        __debug "Found an installation of Postman."
        echo ${_TRUE_};
    else
        __debug "Did not find a Postman installation."
        echo ${_FALSE_};
    fi
}

function get_installed_version {
    local LOCATION=$1;
    local PKG_JSON_LOCATION=${LOCATION}/app/resources/app/package.json;

    __debug "Fetching version of currently installed Postman from location: " ${LOCATION};

    if [[ $(is_installed "${LOCATION}") == $_TRUE_ ]]; then
        local INSTALLED_VERSION=$(cat ${PKG_JSON_LOCATION} | \
            python -c "import sys, json; print(json.load(sys.stdin)['version'])");
        __debug "Postman is installed! The version is: " ${INSTALLED_VERSION};
        echo ${INSTALLED_VERSION};
    else
        __debug "Cannot get installed version: Postman is not installed!";
        echo "";
    fi
}

function get_latest_version {
    __debug "Fetching the latest version from Postman's app release server";
    local LATEST_VERSION=$(curl -s 'https://dl.pstmn.io/changelog?channel=stable&platform=linux' | \
        python -c "import sys, json; print(json.load(sys.stdin)['changelog'][0]['name'])");
    __debug "Latest version is: " ${LATEST_VERSION};
    echo ${LATEST_VERSION};
}

function check_update {
    # if installed, find out the installed version.
    if [[ $(is_installed "${LOCATION}") == $_TRUE_ ]]; then
        local INSTALLED_VERSION=$(get_installed_version "${LOCATION}");
        local LATEST_VERSION=$(get_latest_version);
        if [[ ${LATEST_VERSION} != ${INSTALLED_VERSION} ]]; then
            echo "Update available!"
            echo "    Latest version      : " ${LATEST_VERSION};
            echo "    Installed version   : " ${INSTALLED_VERSION};
        else
            echo "Great, you have the latest version installed!";
        fi
    else
        echo "No local installation of Postman found!";
    fi
}

function get_current_arch {
    if [ $(uname -m) == 'x86_64' ]; then
        echo "64";
    else
        echo "32";
    fi
}

function install_postman {
    local ARCH=$(get_current_arch);
    local TMP_DIR="/tmp/_postman_tmp";
    local TARBALL="${TMP_DIR}/postman.tar.gz";
    local LATEST_VERSION=$(get_latest_version);
    local LATEST_VERSION_URL="https://dl.pstmn.io/download/version/${LATEST_VERSION}/linux${ARCH}";

    mkdir -p ${TMP_DIR};

    __debug "Downloading from URL:" ${LATEST_VERSION_URL};
    curl -o "${TARBALL}" "$LATEST_VERSION_URL";

    _UPD_PM_RETURN=$(pwd);
    cd ${TMP_DIR};
    tar -xf ${TARBALL};

    # Make sure that the Postman executable is available in the extracted folder.
    if [[ -x "${TMP_DIR}/Postman" ]]; then
        mkdir -p ${LOCATION};

        # remove the existing location
        rm -r ${LOCATION};

        # move the temporary directory to the actual location.
        mv "${TMP_DIR}/Postman" ${LOCATION};
    else
        echo "Oops, we might have a corrupt archive downloaded. Aborting installation.";
    fi
}

function update_postman {
    if [[ $(is_installed "${LOCATION}") == $_FALSE_ ]]; then
        echo "Postman is not installed in ${LOCATION}. No update can be performed.";
    else
        install_postman;
    fi
}

function _parse_subcommand_opts {
    local ALL_ARGS=$1;
    __debug "Parsing subcommand opts," ${ALL_ARGS}
    while getopts ":l:" SUB_OPT; do
        case ${SUB_OPT} in
            l )
                LOCATION=${OPTARG};
                ;;
        esac
    done
}

# Parse options to the command
while getopts ":h" OPTION; do
    case ${OPTION} in
        h )
            usage;
            ;;
        \? )
            echo "Invalid Option: -$OPTARG" 1>&2;
            exit 1;
            ;;
    esac
done
shift $((OPTIND -1))

# Extract & remove the subcommand from the list of CLI args
SUB_CMD=$1; shift;

# parse the rest of the args as options.
_parse_subcommand_opts $@;

case "$SUB_CMD" in
    check)
        check_update;
        ;;
    update)
        update_postman;
        ;;
    install)
        install_postman;
        ;;
    "")
        usage;
        ;;
    \? )
        echo "Invalid Sub-Command: $SUB_CMD" 1>&2;
        usage;
        exit 1;
        ;;
esac
