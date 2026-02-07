====
Argo
====

1. (If offsite) Connect to Argonne VPN (Argo requires ANL network/VPN).

2. Go to https://argo.anl.gov/ and authenticate.

2.5. (First-time setup, Conda option) Create and activate a Conda env for
     ``argo-proxy``, then install it::

        conda create -n argo-proxy python=3.11 -y
        conda activate argo-proxy
        python -m pip install --upgrade pip
        pip install argo-proxy

     (After this, you’ll use ``conda activate argo-proxy`` instead of
     ``source ~/.venvs/argo-proxy/bin/activate``.)

3. Start the Argo proxy in Terminal and leave it running:

   * If using Conda::

        conda activate argo-proxy
        argo-proxy

   * If using the original ``venv`` approach::

        source ~/.venvs/argo-proxy/bin/activate
        argo-proxy

4. Start VS Code.

   * Show the Secondary Side Bar: View → Appearance → Secondary Side Bar.
   * Open Continue (Argo UI): Cmd+Shift+P → Continue: Focus on Continue View.
   * Select an Argo model in Continue (e.g., ``Argo GPT-4o``) and use Chat/Agent.

5. When done, stop the proxy: go to the Terminal window running it and press
   Ctrl+C.
