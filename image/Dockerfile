# DeGenRESOLVE desktop image for GitHub Codespaces.
#
# Built by .github/workflows/build-image.yml with devcontainers/ci, which layers the
# desktop-lite feature (Fluxbox + TigerVNC + noVNC on port 6080) on top of this file
# (see image/.devcontainer/devcontainer.json), runs the demo-data self-test, and only
# then pushes to ghcr.io.
#
# To release a new DeGenRESOLVE version: change DEGENRESOLVE_TAG and DEGENRESOLVE_COMMIT
# below, and the image tag in .devcontainer/devcontainer.json.
FROM mcr.microsoft.com/devcontainers/base:bookworm

ARG DEGENRESOLVE_REPO=https://github.com/Shoaib-Saikat/DeGenRESOLVE-ONT-Sequencing-Data-Analyzer.git
ARG DEGENRESOLVE_TAG=v1.0.1
ARG DEGENRESOLVE_COMMIT=b50a3d7bc57f5125757a73966bea09625a014cd9

LABEL org.opencontainers.image.source="https://github.com/Shoaib-Saikat/DeGenRESOLVE-online" \
      org.opencontainers.image.title="DeGenRESOLVE online" \
      org.opencontainers.image.description="DeGenRESOLVE ONT consensus pipeline with its validated conda environment and a browser desktop, for GitHub Codespaces." \
      org.opencontainers.image.licenses="MIT"

# Chromium serves two purposes: NanoPlot 1.47 exports its PNG plots through kaleido 1.x,
# which drives a headless Chrome (the reference run used one too), and the GUI opens the
# NanoPlot/Qualimap HTML reports with xdg-open. Plus a file manager for "open folder",
# window control, X utilities, and the GL/X libraries Qt's xcb plugin may load.
RUN apt-get update \
 && export DEBIAN_FRONTEND=noninteractive \
 && apt-get install -y --no-install-recommends \
      chromium pcmanfm xdg-utils wmctrl x11-utils zip unzip procps \
      libgl1 libegl1 libglx-mesa0 libgl1-mesa-dri libxkbcommon-x11-0 \
      libxcb-icccm4 libxcb-image0 libxcb-keysyms1 libxcb-randr0 libxcb-render-util0 \
      libxcb-shape0 libxcb-xinerama0 libxcb-xkb1 libnss3 libasound2 \
      fonts-dejavu-core fonts-liberation \
 && apt-get clean && rm -rf /var/lib/apt/lists/* \
 && printf '%s\n' 'export CHROMIUM_FLAGS="$CHROMIUM_FLAGS --no-sandbox --disable-dev-shm-usage"' \
      > /etc/chromium.d/zz-container-flags

# Miniforge's conda, used to materialise the explicit lock file exactly as env.lock's own
# header instructs ("conda create --name <env> --file <this file>").
ENV DEGENRESOLVE_ENV=/opt/conda/envs/degenresolve \
    DEGENRESOLVE_HOME=/opt/degenresolve \
    BROWSER_PATH=/usr/bin/chromium
RUN curl -fsSL -o /tmp/miniforge.sh \
      https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh \
 && bash /tmp/miniforge.sh -b -p /opt/miniforge \
 && rm /tmp/miniforge.sh

# DeGenRESOLVE itself, pinned to the release tag and verified against the expected commit.
RUN git clone --branch "${DEGENRESOLVE_TAG}" --depth 1 "${DEGENRESOLVE_REPO}" "${DEGENRESOLVE_HOME}" \
 && test "$(git -C "${DEGENRESOLVE_HOME}" rev-parse HEAD)" = "${DEGENRESOLVE_COMMIT}" \
 && rm -rf "${DEGENRESOLVE_HOME}/.git" \
 && printf 'tag=%s\ncommit=%s\nrepo=%s\n' "${DEGENRESOLVE_TAG}" "${DEGENRESOLVE_COMMIT}" "${DEGENRESOLVE_REPO}" \
      > "${DEGENRESOLVE_HOME}/ONLINE_BUILD_INFO"

# The exact validated environment: env.lock is the @EXPLICIT conda lock the published results
# were produced with (samtools/bcftools 1.24, minimap2 2.31, Qualimap, NanoPlot, PyQt5, ...).
# Then install the app into it the same way the offline bundle does (pip -e, no deps).
RUN /opt/miniforge/bin/conda create -y -p "${DEGENRESOLVE_ENV}" --file "${DEGENRESOLVE_HOME}/env.lock" \
 && /opt/miniforge/bin/conda clean -a -y \
 && "${DEGENRESOLVE_ENV}/bin/python" -m pip install --no-deps --no-build-isolation --quiet -e "${DEGENRESOLVE_HOME}" \
 && chmod -R a+rwX "${DEGENRESOLVE_HOME}"

# The conda env is NOT put on the global PATH, so the desktop (noVNC/websockify, dbus,
# Fluxbox) keeps using system binaries. The degenresolve-* launchers prepend it themselves,
# and interactive terminals get it from bash.bashrc / zshrc.
ENV QTWEBENGINE_DISABLE_SANDBOX=1 \
    QTWEBENGINE_CHROMIUM_FLAGS="--no-sandbox --disable-dev-shm-usage --ignore-gpu-blocklist" \
    LIBGL_ALWAYS_SOFTWARE=1 \
    QT_X11_NO_MITSHM=1
RUN for rc in /etc/bash.bashrc /etc/zsh/zshrc; do \
      [ -f "$rc" ] && printf '\n# DeGenRESOLVE conda environment\nexport PATH="%s/bin:$PATH"\n' "${DEGENRESOLVE_ENV}" >> "$rc"; \
    done; true

COPY bin/ /usr/local/bin/
RUN chmod +x /usr/local/bin/degenresolve-*

# Desktop session start-up: launches DeGenRESOLVE together with the desktop. The desktop-lite
# feature only adds the Fluxbox files that don't exist yet, so this one is kept.
COPY fluxbox-startup /home/vscode/.fluxbox/startup
RUN chmod +x /home/vscode/.fluxbox/startup \
 && chown -R vscode:vscode /home/vscode/.fluxbox

# Fail the build early if anything the pipeline or GUI needs is missing.
RUN export PATH="${DEGENRESOLVE_ENV}/bin:${PATH}" \
 && python -c "import pysam, Bio, numpy, PyQt5.QtWidgets, PyQt5.QtWebEngineWidgets; print('python modules ok')" \
 && samtools --version | head -1 \
 && bcftools --version | head -1 \
 && command -v vcfutils.pl \
 && echo "minimap2 $(minimap2 --version)" \
 && echo "porechop $(porechop --version)" \
 && NanoPlot --version \
 && (seqtk 2>&1 | grep -i version) \
 && java -version 2>&1 | head -1 \
 && command -v qualimap \
 && chromium --version
