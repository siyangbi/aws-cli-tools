#!/usr/bin/env bash
set -e
set +x

type aws >/dev/null 2>&1 || { echo >&2 "aws cli is required but it is not installed."; exit 1; }
type nslookup >/dev/null 2>&1 || { echo >&2 "nslookup is required but it is not installed."; exit 1; }

# usage
usage () {
  echo "Usage: ${0##*/} [-h|--help] <domain> <port> <security-group-id>"
  exit 0
}

# read arguments
for ARG in $@
do
    case "$1" in
        -h|--help) usage; ;;
        # *) shift; ;;
    esac
done

if [ $# -lt 3 ]; then usage; fi

DOMAIN=$1
PORT=$2
SG_ID=$3

allow_ip () {
    CIDR="${1}/32"
    echo "Querying AWS for ${CIDR} port ${PORT} in security group ${SG_ID} "
    if aws ec2 describe-security-groups --group-ids ${SG_ID} --query 'SecurityGroups[*].IpPermissionsEgress[?FromPort==`'"${PORT}"'`].IpRanges' --output text | grep ${CIDR}
    then
        echo "Allow rule is already there."
    else
        echo "Allow rule is not there yet. Adding it now..."
        aws ec2 authorize-security-group-egress --group-id ${SG_ID} --ip-permissions '[{"IpProtocol": "tcp", "FromPort": '"${PORT}"', "ToPort": '"${PORT}"', "IpRanges": [{"CidrIp": "'"${CIDR}"'"}]}]'
    fi
}

echo "Performing nsLookup for ${DOMAIN}"
nslookup ${DOMAIN} | awk '/^Address: / { print $2 }' | while read IP
do
    allow_ip $IP
    echo ;
done