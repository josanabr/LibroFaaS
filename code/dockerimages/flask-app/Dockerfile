FROM josanabr/python3-armv7l:1.0.3
RUN pip3 install --no-cache-dir redis
WORKDIR /code
COPY app.py .
EXPOSE 5000
ENTRYPOINT [ "python3" ]
CMD [ "/code/app.py" ]
