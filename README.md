# code-oss-workspace

```bash
wget https://raw.githubusercontent.com/cgruver/code-oss-workspace/refs/heads/main/che-code-editor.yaml
oc create configmap che-code-copilot --from-file=che-code-editor.yaml -n devspaces
oc label configmap che-code-copilot app.kubernetes.io/part-of=che.eclipse.org app.kubernetes.io/component=editor-definition -n devspaces
```

```bash
podman build -f build/dockerfiles/linux-libc-ubi9.Dockerfile -t linux-libc-ubi9 .
export DOCKER_BUILDKIT=1
podman build -f build/dockerfiles/assembly.Dockerfile -t nexus.clg.lab:5002/dev-spaces/che-code:latest .
```

```bash
cp -r code checode-compilation
rm -rf /checode-compilation/node_modules
npm install --force
NODE_ARCH=$(echo "console.log(process.arch)" | node)
NODE_VERSION=$(cat /checode-compilation/remote/.npmrc | grep target | cut -d '=' -f 2 | tr -d '"')
mkdir -p /checode-compilation/.build/node/v${NODE_VERSION}/linux-${NODE_ARCH}
cp /usr/bin/node /checode-compilation/.build/node/v${NODE_VERSION}/linux-${NODE_ARCH}/node
NODE_OPTIONS="--max-old-space-size=8192" ./node_modules/.bin/gulp vscode-reh-web-linux-${NODE_ARCH}-min
cp -r ../vscode-reh-web-linux-${NODE_ARCH} /checode
mkdir -p /checode/ld_libs
find /usr/lib64 -name 'libbrotli*' 2>/dev/null | xargs -I {} cp -t /checode/ld_libs {}
find /usr/lib64 -name 'libnode.so*' -exec cp -P -t /checode/ld_libs/ {} +
find /usr/lib64 -name 'libz.so*' -exec cp -P -t /checode/ld_libs/
chmod a+x /checode/out/server-main.js
chgrp -R 0 /checode && chmod -R g+rwX /checode
```


```bash
cat << EOF | oc apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: vscode-editor-configurations
  namespace: devspaces
  labels:
    app.kubernetes.io/part-of: che.eclipse.org
    app.kubernetes.io/component: workspaces-config
data:
  settings.json: |
    {
      "chat.disableAIFeatures": false,
      "chat.commandCenter.enabled": true
    }
EOF
```

## Build github-chat
```bash
git clone https://github.com/cgruver/vscode-copilot-chat.git

npm install

npx @vscode/dts dev && mv vscode.proposed.*.ts src/extension

npx tsx .esbuild.ts

vsce package

```


Notes:

workbench.action.chat.triggerSetupForceSignIn

https://github.com/SoloJiang/vscode-with-copilot-config

