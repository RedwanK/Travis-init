FROM ansible/ansible:latest

COPY id_rsa ./id_rsa
COPY playbook.yml ./playbook.yml
COPY roles ./roles
COPY inventories ./inventories

RUN chmod 400 ./id_rsa
RUN ansible-playbook -i inventories/setup.yml playbook.yml
