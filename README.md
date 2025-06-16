# ansible-k8s-cluster
<!-- TREEVIEW START -->
.
├── README.md
├── ansible.cfg
├── inventory.ini
├── roles
│   ├── helm_addons
│   │   ├── files
│   │   └── tasks
│   │       └── main.yml
│   ├── helm_install
│   │   ├── files
│   │   └── tasks
│   │       └── main.yml
│   ├── kubeadm_init
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── templates
│   │       └── init-config.yaml.j2
│   ├── kubeadm_join
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── templates
│   │       └── join-config.yaml.j2
│   ├── privision_admin
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── vars
│   │       └── main.yml
│   └── setup_k8s
│       ├── files
│       ├── handlers
│       │   └── main.yml
│       ├── tasks
│       │   └── main.yml
│       └── vars
│           └── main.yml
└── site.yml
<!-- TREEVIEW END -->
