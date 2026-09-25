Este mod de strikers evade el cambio de teclado a mando en el port 0, (teclado = port 0) (mando = port 1,2,3)
con este mod el player 1 siempre va a ser el teclado y los restantes mandos, si quieren agregar este parche en otros SO deben modificar lo siguiente:

descargar la descompilacion del usuario yannicksuter
https://github.com/yannicksuter/smstrikers-decomp

ubicarse en la ruta strikers\smstrikers-port\src\platform y buscar el archivo input.cpp

en la linea 610 agregar este parche:

```cpp
 SDL_JoystickID s_padId[PAD_CHANMAX];
 bool s_padsLooked = false;
// STRIKERS_PAD_PORT: fuerza el primer mando que SDL detecta a un puerto específico, en vez de
// dejarlo donde SDL lo asigne por defecto (normalmente 0, chocando con el teclado, que está fijo
// en el puerto 0). STRIKERS_PAD_PORT=1 reclama el primer mando para el puerto 1 (Jugador 2) en
// cuanto aparece, y no vuelve a tocarlo después.
void apply_forced_pad_port()
{
    const char* v = input_cfg("STRIKERS_PAD_PORT");
    if (v == nullptr || *v == '\0')
        return;
    const long wanted = std::strtol(v, nullptr, 10);
    if (wanted < 0 || wanted >= (long)PAD_CHANMAX)
        return;
    static bool claimed = false;
    if (claimed || PADCount() == 0)
        return;
    if (PADGetIndexForPort((u32)wanted) >= 0)
    {
        claimed = true;
        return;
    }
    PADSetPortForIndex(0, (u32)wanted);
    OSReport("[port] input: STRIKERS_PAD_PORT: claimed controller 0 for port %ld\n", wanted);
    claimed = true;
}

 void poll_controllers(bool report)
 
 {
    apply_forced_pad_port();
     for (u32 p = 0; p < PAD_CHANMAX; p++)
```


  Guardan y compilan el proyecto, creditos a los usuarios yannicksuter y new-coke donde tome la descompilacion y el archivo strikers-settings.exe

