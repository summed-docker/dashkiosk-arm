FROM arm32v7/node:8

ARG DashKiosk_Version=v2.7.3

RUN npm install -g bower grunt-cli
RUN apt-get -qq update && apt-get install -qq gifsicle libjpeg-progs optipng libavahi-compat-libdnssd-dev git

RUN git clone -b $DashKiosk_Version --depth 1 https://github.com/vincentbernat/dashkiosk.git /dashkiosk

WORKDIR /dashkiosk

ENV NPM_CONFIG_LOGLEVEL warn

RUN npm update
RUN npm install
RUN npm audit fix
RUN grunt
RUN cd dist && npm install --production
RUN rm -rf ../node_modules ../build
RUN npm cache clean --force

# We use SQLite by default. If you want to keep the database between
# runs, don't forget to provide a volume for /database.
VOLUME /database

ENV NODE_ENV production
ENV port 8080
ENV db__options__storage /database/dashkiosk.sqlite

EXPOSE 8080

#CMD [ "node", "/dashkiosk/dist/server.js" ]

CMD service dbus start \
    && avahi-daemon --no-drop-root --daemonize \
    && exec node /dashkiosk/dist/server.js "$@"