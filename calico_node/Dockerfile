# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
FROM debian:10-slim
LABEL maintainer "Casey Davenport <casey@tigera.io>"

# Set the minimum Docker API version required for libnetwork.
ENV DOCKER_API_VERSION 1.21

# Install remaining runtime deps required for felix from the global repository
RUN apt-get update && apt-get install -y \
    ipset \
    iputils-arping \
    iputils-ping \
    iputils-tracepath \
    # Need arp 
    net-tools \ 
    conntrack \ 
    runit \ 
    # Need kmod to ensure ip6tables-save works correctly
    kmod \ 
    # Need netbase in order for ipset to work correctly 
    # See https://github.com/kubernetes/kubernetes/issues/68703
    netbase \ 
    # Also needed (provides utilities for browsing procfs like ps) 
    procps \    
    ca-certificates

# Install iptables from buster to get version 1.8.2
# Similarly for iproute2, we want a more recent version for BPF support.
RUN echo 'APT::Default-Release "stable";' > /etc/apt/apt.conf.d/99defaultrelease
RUN echo 'deb     http://ftp.de.debian.org/debian/    buster main contrib non-free' > /etc/apt/sources.list.d/buster.list
RUN apt-get update && apt-get install -y -t buster \
    ipset \
    iptables \
    iproute2

# Set the iptables and ip6tables binaries to link to the legacy backend
# version of the commands, by default they link to the nft backend.
RUN update-alternatives --set iptables /usr/sbin/iptables-legacy
RUN update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy

# Copy in the filesystem - this contains felix, bird, calico-bgp-daemon etc...
COPY filesystem /